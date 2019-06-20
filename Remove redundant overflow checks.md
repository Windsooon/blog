---
title: Remove redundant overflow checks
date: 2018-09-21 11:49:00
tags: python open source
---

[Day One: Coroutine or Coroutine Function](http://windsooon.github.io/2018/09/19/Day%20One/)

Today I'm going to review [pull_8757](https://github.com/python/cpython/pull/8757), My selection criteria it's the PR should be 

1. Neither too new or too old
2. Passed most of the tests and been labeled **awaiting review** 
3. I can review it in a day.

(Another python contributor **Tal Einat** gave me so many advises in my yesterday post, Thank you so much, Tal.)

### Step 1: discussion
I Searched the [issue discussion](https://bugs.python.org/issue34397)

> Max size of list and tuples is limited by **PY\_SSIZE\_T\_MAX / sizeof(PyObject\*)**, so the sum of any two list/tuples sizes always <= **PY\_SSIZE\_T\_MAX** if **sizeof(PyObject\*)** > 1, which seems true for all supported (existing?) platforms.
It means that overflow checks in app1, ins1, list\_concat and tupleconcat are redundant and can be safely removed.

It looks like we overflow checks in app1, ins1, list\_concat function, Tim Peters already reviewed some of the come so now is my turn.

### Step 2: commit messages and files changed
#### commit messages:
> Remove redundant overflow checks in list and tuple implementation

#### files changed:

![pull_8757](https://raw.githubusercontent.com/Windsooon/blog/master/imgs/30days/day2_pull_8757.png)

I'mo familiar with C, it's kinda uncomfortable to review code I'm not familiar with. However, this is also a good way to learn c in a real project, right? let's dive into the code.

### Step 3: dive into the code
##### Search context

The changed locate at [function ins1](https://github.com/python/cpython/blob/2fc46979b8c802675ca7fd51c6f2108a305001c8/Objects/listobject.c#L255) which lack of comments.

    ins1(PyListObject *self, Py_ssize_t where, PyObject *v)
    {
        Py_ssize_t i, n = Py_SIZE(self);
        ...
        /* Check the list is full or not
        if (n == PY_SSIZE_T_MAX) {    
            PyErr_SetString(PyExc_OverflowError,    
                "cannot add more objects to list");    
            return -1;    
        }
    }

After I search the call stack, this method is used to insert an item in a list. For example:

    lst = []
    # We will call function ins1 when inserting value in a list
    lst.insert(10, 0)

BTW, what is **PY\_SSIZE\_T\_MAX** actually, I found [sys.maxsize in the document](https://docs.python.org/3/library/sys.html#sys.maxsize)

> An integer giving the maximum value a variable of type Py_ssize_t can take. It’s usually 2\*\*31 - 1 on a 32-bit platform and 2**63 - 1 on a 64-bit platform.

This PR said the max number of a list should be **PY_SSIZE_T_MAX / sizeof(PyObject\*)** instead of **PY\_SSIZE_T_MAX** so we overcheck in the code.

#### Step 4: Prove speculation
W can find The maxium number in the list in [list_resize](https://github.com/python/cpython/blob/master/Objects/listobject.c#L34)

    new_allocated = (size_t)newsize + (newsize >> 3) + (newsize < 9 ? 3 : 6);
    if (new_allocated > (size_t)PY_SSIZE_T_MAX / sizeof(PyObject *)) {
        PyErr_NoMemory();
        return -1;
    }

And According to [PEP353](https://www.python.org/dev/peps/pep-0353/#specification)

> A new type **Py\_ssize\_t** is introduced, which has the same size as the compiler's **size\_t** type, but is signed. It will be a typedef for **ssize\_t** where available. 

I don't know why we use siez_t here, so I asked at [python.zulipchat](https://python.zulipchat.com/#narrow/stream/116742-core.2Fhelp/topic/pull_8757), Ammar Askar gave me a great answer. So, after that, I left some comments on the [PR](https://github.com/python/cpython/pull/8757/files#diff-cde4fb3109c243b2c2badb10eeab7fcd), I suggested to keep the error messages add some edit.

### Conclusion
I’m not afraid of C that much, I reviewed the C syntax and I found it’s much easier than I learned before, I also found another challenge for myself, find the difference of sys.max, PY_SSIZE_T_MAX and SIZE_MAX.


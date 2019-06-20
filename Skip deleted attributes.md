---
title: Skip deleted attributes while calling reset_mock
date: 2018-09-22 15:51:00
tags: python open source
---

Today I'm going to review [pull_9302](https://github.com/python/cpython/pull/9302)


### Step 1: discussion
> When using a mock and deleting an attribute reset_mock cannot be used anymore since it tries to call reset_mock on the _deleted sentinel value.

    from unittest.mock import MagicMock
    mock = MagicMock()
    mock.a = 'test'
    del mock.a
    mock.reset_mock()

Gives:

    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
      File "/usr/lib/python3.5/unittest/mock.py", line 544, in reset_mock
        child.reset_mock(visited)
    AttributeError: '_SentinelObject' object has no attribute 'reset_mock'

### Step 2: commit messages and files changed
#### commit messages:
> Skip deleted attributes while calling reset_mock

#### files changed:

![pull_9302](https://raw.githubusercontent.com/Windsooon/blog/master/imgs/30days/day3_pull_9302.png)

So what actually reset_mock do?

### Step 3: dive into the code

    def reset_mock(self,  visited=None,*, return_value=False, side_effect=False):
        "Restore the mock object to its initial state."
        ...

        for child in self._mock_children.values():
            if isinstance(child, _SpecState):
                continue
            child.reset_mock(visited)

        ret = self._mock_return_value
        if _is_instance_mock(ret) and ret is not self:
            ret.reset_mock(visited)

> reset_mock(*, return_value=False, side_effect=False)
> The reset_mock method resets all the call attributes on a mock object:

    >>>
    >>> mock = Mock(return_value=None)
    >>> mock('hello')
    >>> mock.called
    True
    >>> mock.reset_mock()
    >>> mock.called
    False

### Step 4: Fixed it
But why we use _deleted instead of None? The PR propose a fix. Before understanding the fix, we have to understand Mock and Sentinel in python. 

> Mock and MagicMock objects create all attributes and methods as you access them and store details of how they have been used. You can configure them, to specify return values or limit what attributes are available, and then make assertions about how they have been used:

And from [Sentinel](https://www.revsys.com/tidbits/sentinel-values-python/)

> Sometimes it is necessary to differentiate between an argument that has not been provided, and an argument provided with the value None. For that purpose, we create what's called a 'sentinel value'.

We implement _SentinelObject class:

    class _SentinelObject(object):
        "A unique, named, sentinel object."
        def __init__(self, name):
            self.name = name

        def __repr__(self):
            return 'sentinel.%s' % self.name

        def __reduce__(self):
            return 'sentinel.%s' % self.name

    ...
    sentinel = _Sentinel()

    DEFAULT = sentinel.DEFAULT
    _missing = sentinel.MISSING
    _deleted = sentinel.DELETED
 
After we delete an attr in the code, child become sentinel.DELETED instead of None. So I propose a better test case:

    def test_attribute_deletion_reset_mock(self):
        mock = Mock()
        mock.attr = 'test'
        del mock.attr
        mock.reset_mock()
        self.assertFalse(hasattr(mock, 'attr'))

### Conclusion
Instead of review PR every day, I found that write topic article depends on the pr/issue would be more helpful to readers. That is what I'm going to do next.

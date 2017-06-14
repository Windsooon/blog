---
title: What is Process
---
# What is Process 

When I first know process，I thought I understand it. But then I meet multiprocess, multithread in mutliprocess, coroutine.These start confuce me.Because I don't know how computer inside duel with them.

Let's have a quick look.

>In computing, a process is an instance of a computer program that is being executed. It contains the program code and its current activity. Depending on the operating system (OS), a process may be made up of multiple threads of execution that execute instructions concurrently

> --Wikipedia

This is 100% correct but I don't know what this mean at the beginning, this margin is too small to contain.

[what?](http://)


At this article, I try to explain how process really implement in the computer.

### Laundry Example
let's look an example

* First, Your wife/husband ask you to do the laundry,  
you choose the clothes need wished. 
* Second, check if any clothes left in the washing machine, if have any, take them off the machine. 
* Third, put the dirty clothes into it.

Computer process just like do the laundry.

* First, a father process will create a child process and ask it to execute the same code as father process does.The different bewteen father process and child process is they have their own stack. 
* Second, check if CPU running any process, if it does, the child process has to wait after that process finish, because CPU can only run one process in the same time
* Third, the child process begin to run.


### Hello world program

This program will print "Hello world" in the console.
C:

    #include <stdio.h>
 
    int main()
    {
      printf("Hello world\n");
    }

Python:

    print("Hello world")
    
Relax if you not familiar with code, C language code still scare me sometimes :D.
     
SO when you run 

    user>gcc -o hello hello.c
    
Or

    user>python hello.py

At this moment, 

your terminal which is also a process will create a child process,

>When a process is created, it is almost identical to its parent. It receives a (logical) copy of the parent's address
space and executes the same code as the parent, beginning at the next instruction following the process
creation system call.

In short, The child process were tell to compile the Hello world program to machine code.

So now, your computer is running two processes.
(In fact, your computer will run some processes after you open it, you can use PS -A when your computer base on *nix or tasklist when you using Windows to list all of them. But now, We don't have to care about these processes right.)

CPU only understand 0 and 1. In most case, no matter what computer languages you use, for example, C, Python or Java. The code will finally become 0 and 1 then let CPU to execute.  
Let's assume the "Hello world" program finally been convert to

    000 001 011 111
    
and deliver to CPU.


















    
    

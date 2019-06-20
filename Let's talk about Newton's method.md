---
title: Let's talk about Newton's method (to be finished)
date: 2019-04-19 20:57:00
tags: math leetcode
draft: True
---

### Basic
When I try to [solve some Leetcode questions](https://leetcode.com/Windson/) like [Valid Perfect Square](https://leetcode.com/problems/valid-perfect-square/) and [Sqrt(x)](https://leetcode.com/problems/sqrtx/) I found some discuss mention about Newton's method. We use this method to find the root of a equation close enough. I guess I learned that in my college but I totally forgot. In this article I will explain it in the hard way.

### What Newton's method for

When we want to solve an equation, sometimes it's very find the answer. For instance, `y = x - 6`, you can see when x equal to 6, we can get the expect answer. Sometims it's kinda hard. `y = x*x - x - 10`, you can still use formula to find the answer. But sometims pretty hard like `x*x*x*x - 3*x*x + 10`. Ususally we can use binary search to find the approximated answer which it is quite useful, we all know that binary search is not that easy to write it correctly. 

![function]()

We guess the answer, then we apporch it step by step.

### How it works

We have a function `x*x-4=0` and we don't know x, so we choose `4` as the first answer then move to the correct answer. (we will show you why we choose 4 here and how we move to the correct answer later). 



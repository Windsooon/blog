---
title: How to make Python GIL better
date: 2017-07-16 20:00:00
tags: GIL Python
---

1. How os system schele work
2. Now with GIL
2. multicore
3. 

I assume you know what is GIL in python and some basic knowledgt to use the python threadding module. If you don't, you may have a look at the article here. Let's recap the os system

### How os system schele work
It's not a easy topic, we speed quite a long time to search for a prefect scheduling policy. Since Linux 2.6.23, the default scheduler is CFS. What is CFS and why we need it? 

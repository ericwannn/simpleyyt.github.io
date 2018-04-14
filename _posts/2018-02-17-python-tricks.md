---
layout: post
category: Programming
title: Python tricks & traps
tagline: by Snail
tags: 
  - python
  - programming
published: true
---

In this article I list out some tricks in Python programming. I will keep updating this post each time I find anything interesting.

# Traps in default value

```python
def append_list(num, res=[]):
  res.append_list(num)
  return res

l1 = append_list(1)
l2 = append_list(2)
l3 = append_list(3)

print(l1)
print(l2)
print(l3)
```

The code seem fine because every time the function itself would initialize an empty list and append a new value to it. However the result is very surprising:

```python
[1, 2, 3]
[1, 2, 3]
[1, 2, 3]
```

This is because the default value, which is an empty list here keeps using the same address in the memory. Whenever value is specified for `res`, it simply use that same list on the specific memory address. These three list, `l1`, `l2` and `l3` are thus actually manipulating the same list.

The correct way to do this is as follows, which would kinda avoid this problem.

```python
def append_list(num, res=None):
    if res == None:

l3 = append_list(3)

print(l1)
print(l2)
print(l3)
```

<!--more-->

# Python closure

```python
def squares():
    res = []
    for i in range(3):
        def make_square(x):
            return i ** x
    res.append(make_square)
    return res

for square in squares():
    print(square(2))
```

The result is surprising because the functions in another function will not check the value of `i` until it runs. So it only looks at the value of `i` when all three `make_square` functions are ready, at which moment the value of `i` is already `3`.
One way to avoid the problem is to pass the index value explicitly to the function.

```python
def squares():
    res = []
    for i in range(3):
        def make_square(x, i=i):
            return i ** x
    res.append(make_square)
    return res

for square in squares():
    print(square(2))
```

More details about closure in Python are available [here](https://ericwannn.github.io/2018/04/12/closure-python/).


# Enumerate

# To float before division

`from __future__ import division`

# is or ==

# why __init__
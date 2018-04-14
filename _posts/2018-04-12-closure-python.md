---
layout: post
category: Programming
title: Closure in Python by examples
tagline: by Snail
tags: 
  - python
  - programming
published: true
---
# Overview

1. *Closure* is an important feature of Python which protect the variables defined in different places against poisoning each other.
2. An common usage of closure is defining an function within another function. The variables are thus defined in different scope, some covering others. So the tricks and traps with closure is talking about how can we make it convenient by using this features and avoid faults that could be caused by it.
3. Closure could directly use variables defined outside of it and within the scope surrounding it. When it looks for an variable, it starts searching from local variables. When there is no local variables with the same name existed, it extends the searching space to the closure surrounding it.
4. Using variables and assigning new values to variables are different. When it tries assign an value to a variable, it starts from local variables as well but it would create a local copy of it if the target doesn't exist.

We will walk through these issues with some examples.

<!--more-->

## Closure could *use* variables defined outside the scope

To understand what a closure is and how variables exist among different scopes, let's take a look at this example.

```python
def sort_priority(values, group):
    def helper(x):
        if x in group:
            return (0, x)
        return (1, x)
    values.sort(key=helper)

def main():
    numbers = [8, 3, 1, 2, 5, 4, 7, 6]
    group = {2, 3, 5, 7}
    sort_priority(numbers, group)
    print(numbers)

if __name__ == "__main__":
    main()
```

This function is very simple. It sorts an array and checks another list at the same time, where the numbers in the list are of higher priority and will be sorted in the first order.
The expected result would be as follows.

```python
[2, 3, 5, 7, 1, 4, 6, 8]
```

This function could work because `helper` is able to use the value of `group`. We don't need to bother passing `group` into `helper` even it is defined outside of it because the whole function `helper` function is defined within the environment of `sort_priority` where `group` is defined.

## Problem occurs when closure *assign values* to variables outside of it

Say if we want this function to return a flag indicating whether it finds anything in `group` that shall be given high priority. We can simply add another boolean variable `found`.

```python
def sort_priority2(values, group):
    found = False

    def helper(x):
        if x in group:
            found = True
            return (0, x)
        return (1, x)
    values.sort(key=helper)
    return found

def main():
    numbers = [8, 3, 1, 2, 5, 4, 7, 6]
    group = {2, 3, 5, 7}
    found = sort_priority2(numbers, group)
    print("found: %s" % found))
    print(numbers)

if __name__ == "__main__":
    main()
```

If you run this script yourself, you will see something interesting:

```python
found: False
[2, 3, 5, 7, 1, 4, 6, 8]
```

The value of `found` is wrong whereas the `numbers` is correctly sorted! The reason for this is the rule #2 we mentioned earlier. When `helper` assign `True` to `found`, it starts looking for found locally. Certainly, there is no `found` there so it created a local value `found` which is different from the `found` defined in `sort_priority2` (you can examine this by looking at their address).

## Access data in a closure

### `nonlocal` keyword

In Python 3.x, there is an convenient keyword `nonlocal` which works in an complementary way with `global`, declaring that when you looks for `found`, go to upper level closure. So you can do something like this and it should yield the results as expected.

```python
def sort_priority3(values, group):
    found = False

    def helper(x):
        nonlocal found
        if x in group:
            found = True
            return (0, x)
        return (1, x)
    values.sort(key=helper)
    return found

def main():
    numbers = [8, 3, 1, 2, 5, 4, 7, 6]
    group = {2, 3, 5, 7}
    found = sort_priority3(numbers, group)
    print("found: %s" % found))
    print(numbers)

if __name__ == "__main__":
    main()
```

### Encapsulate `helper`

One tip for Python users is that avoiding using `nonlocal` as you can or only use them in small simple functions as the side-effects are hard really hard to trace.

In this case, one smart way to do this is encapsulating `helper`.

```python
class Sorter(object):
    def __init__(self, group):
        self.group = group
        self.found = False

    def __call__(self, x):
        if x in self.group:
            self.found = True
            return (0, x)
        return (1, x)

def main():
    numbers = [8, 3, 1, 2, 5, 4, 7, 6]
    group = {2, 3, 5, 7}
    sorter = Sorter(group)
    numbers.sort(key=sorter)
    print(sorter.found)
    print(numbers)

if __name__ == "__main__":
    main()
```


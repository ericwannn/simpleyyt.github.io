---
layout: post
category: Programming
title: Where to put const in C++
tagline: by Snail
tags: 
  - C++
  - programming
published: true
---

# Problem

Pointer in C++ is confusing. The declaration of pointer makes it worser.   
One classic problem of pointer declaration is as follows. 

```cpp
const int * p1;   // Case 1;  
int * const p2;   // Case 2; Initialization required in practice
```
So here are the problems:  
1. What's the difference between this two statements?   
2. What does `const` constrains on each of them?

<!--more-->

# TL;DR

## Case 1
```cpp
int a = 5;
const int * p1;
p1 = &a;
```
* `p1` can be re-assigned another address but it must be an integer (in this case).
* value of `a` can be changed which will change the value of `*p1` as well (of course)
* Whereas `*p1` cannot be changed directly.


## Case 2

```cpp
int a = 5;
int * const p2 = &a;
```
* `p2` requires immediate initialization at declaration, which points it to a variable. 
* `p2` cannot re-points to another address, which has to be `a` (in this case).
* `*p2` can be changed, which changes value of `a`.
* `a` can has be changed. 


# Analysis
In this section we look into the declarations and see what's happening in there. Let's first try to translate them into human language:

As suggested in *The C++ Programming Language* by *Bjarne*, the declaration of a pointer is split by \*, where the part on the right is the intrinsic property of the pointer and the part on the left describes what does the pointer points to. 

Hence, **Case 1 declares a pointer that points to a const int.**  
**Case 2 declares a const pointer that points to an integer.**  

Now what we got is a pair of a pointer and a value. The problem actually is the relation between them and whether we can manipulate one with the other one.


## Case 1:  

 > `const int * p1;`  
 > A pointer that points to a const int

* Okay to assign `p1` another address

    `p1` here is a free pointer which can points to any variables of type `int` ( or `const int` ). So it's okay to assign it another address as follows:
    ```cpp
    int a = 5;
    const int * p1 = &a;
    int b = 10;
    p1 = &10; // now *p1 = 10
    ```

* Okay to change the value of the variable `p1` points to

    In this case, `p1` points to `a`, which is only modified by keyword `int`. The value of `a` can definitely be changed here, no matter what it is in the declaration of `p1`
    ```cpp
    int a = 5;
    const int * p1 = &a;
    int a = 10; // now *p1 = 10
    ```   

* `*p1` cannot be changed directly

    In declaration of `p1`, it is supposed to point to a constant integer so it's not cool to change the respective value from the side of the pointer.
    ```cpp
    int a = 5;
    const int * p1 = &a;
    *p1 = 10; // Invalid
    ``` 



## Case 2: `int * const p2`

> `int * const p2 = &a;`  
> A const pointer that points to an integer

* `p2` cannot be assigned another address. 

    `const` keyword ensures that it points its initialization variable.
    ```cpp
    int a = 5;
    int * const p2 = &a;
    int b = 10;
    p2 = &b; // Invalid
    ``` 

* Okay to alter the value of `a`  

    The reason is the same as 2nd bullet point in Case 1. `a` isn't constrained by `const`
    ```cpp
    int a = 5;
    int * const p2 = &a;
    a = 10; // *p2 = 10 now
    ```     

* Okay to modify the value of `*p2`

    `p2` constantly points to a address, which shall hold a `int` value in this case so `p2` wouldn't care if the value changes.
    ```cpp
    int a = 5;
    int * const p2 = &a;
    *p2 = 10 // a = 10 now
    ```   
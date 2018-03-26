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
const int * p1;
int * const p2;
```
So here are the problems:  
1. What's the difference between this two statements?   
2. What does `const` constrains in each of them?

# TL;DR

# Analysis

Let's first try to translate them into human language:

As suggested in *The C++ Programming Language* by *Bjarne*, the declaration of a pointer is split by \*, where the part on the right is the intrinsic property of the pointer and the part on the left describes what does the pointer points to. 

Hence, **Line 1 declares a pointer that points to a const int.**  
**Line 2 declares a const pointer that points to an integer.**  

Now what we got is a pair of a pointer and a value. The problem actually is the relation between them and whether we can manipulate one with the other one.


## Case 1: `const int * p1` 


`const` on `p1` only constrains the type of value that `p1` points to must be `int`. It doesn't mean that the value of `p1` cannot be changed. 

Two approaches are available to alter the value of `p1`:

1. Change the value that `p1` points to;
2. Point `p1` to another value.

Whereas it's impossible to assign the pointer to a value of other types. 

```cpp
// Case 1:
// Two approaches to alter a pointer that points to a const int
#include <iostream>
int main(){

    int a = 5;
    int b = 10;
    float c = 15.0;
    const int* p1 = &a;
    std::cout << *p1 << "\n";

    // Approach 1
    // Okay to change the value 
    a = 8;
    std::cout << *p1 << "\n";

    // Approach 2
    // Also cool to re-direct the pointer
    p1 = &b;
    std::cout << *p1 << "\n";

    // Invalid
    // The type must be int 
    p1 = &c;
    std::cout 

    return 0;
}
```
### Conclusion

## Case 2: `int * const p2`

**`int * const p2` makes sure that **
```cpp
// Case 2:
// Two approaches to alter a pointer that points to a const int
#include <iostream>
int main(){
    int a = 5;
    int b = 8;
    int * const p2 = &a;
    std::cout << *p2 << "\n";

    // Valid. Okay to alter the value.
    a = 15;
    std::cout << *p2 << "\n";

    // Invalid. p2 cannot re-point to another value.
    p2 = &b;

    return 0;
}

```

### Conclusion
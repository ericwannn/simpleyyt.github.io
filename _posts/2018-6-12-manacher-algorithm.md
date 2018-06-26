---
layout: post
category: programming
title: Understanding Manacher Algorithm
tagline: by Snail
tags: 
  - algorithm
  - programming 
published: true
---

# Understand Manacher algorithm

Manacher algorithm came across to me recently which solves palindromic substrings finding problem in an efficient way. The code could be really simple (few lines) whereas the idea behind manacher is kinda complex. This article tries to walk you through the basic intuition of Manacher algorithm and how it works.

## Problem description

[This problem](https://leetcode.com/problems/palindromic-substrings/) is a great for better understanding manacher algorithm:

> Given a string, your task is to count how many palindromic substrings in this string.
>
> The substrings with different start indexes or end indexes are counted as different substrings even they consist of same characters.

The problem isn't complicated. We need to find the number of substrings that are palindromic. Let's start by looking at a straight forward approach.

<!--more-->

## Intuition

We ain't gonna talk about brutal search approach because it's not scalable. A good starting point is *center expand approch*. 

This approach is straight-forward. Each character in the string could be a the center of possible palindromic substring (at least the character itself is). We try to expand this character longer by checking if the character on the right is equal to the character on the right.

```python
class Solution(object):
    def countSubstrings(self, S):
        N = len(S)
        ans = 0
        for center in xrange(2*N - 1):
            left = center / 2
            right = left + center % 2
            while left >= 0 and right < N and S[left] == S[right]:
                ans += 1
                left -= 1
                right += 1
        return ans
```

This approach has *O(N^2)* time complexity which isn't good enough. But this idea is very important in manacher algorithm.

## Manacher algorithm

### What it does

1. For each character `i` in the string, it maintains a value `Z[k]` representing the length of the longest the palindromic substring centered at `i`
2. The basic idea is the same as *center expand approach*, where we find the longest palindromic substring centered at `i`
3. If the length of a string is even, there is no center. So some place holders are inserted (usually use #) into the original string which could play as center when the length is not odd. In this way, the odd and even strings are unified.
4. Some calculations can be saved when `k` *is contained in another palindromic string*. 

Bullet point 3 is the key feature that makes manacher algorithm efficient. Let's see how we can save calculations in the case that a character is contained in another palindromic substring.

### Core concepts

Say the string `s` is `ABAABA` and is `@#A#B#A#A#B#A#$` after place holders are inserted. The longest palindromic substring that a character could reach is kept in array `Z`.   
For example, if Z[i] = 2, then s[i - 2 : i + 2] is palindromic.

When we are calculating `Z[i]`, there are four possible situations:

#### 1. character `s[i]` is not contained in any palindromic strings found before

In this case, it's the same as center expand approach where we will try to expand it by checking if the characters on each side are equal.

#### 2. character `s[i]` is not contained in any palindromic strings found before

This is the case which saves your time.   
Since we have already calculated all `Z` values before. If `s[i]` is contained in another palindromic substring which is centered at `center`. So `Z[center] + center > i`.

Since `s[center - Z[center] : center + Z[center]]` is palindromic, we can look back at the values `j` which reflects `i` about `center`. We have already calculated `Z[j]`. There are again, three possible situations.

##### 1. `Z[j] < Z[center] + center - i` 

The longest palindromic string centered at `j` is completed contained in `s[center - Z[center] : center + Z[center]]`.

 `Z[i]` has to be equal to `Z[j]`. Since the substring on `j` is symmetric to that on `i`.

##### 2. `Z[j] > Z[center] + center - i` 

Only part of the longest palindromic string is contained in `s[center - Z[center] : center + Z[center]]`. 

`Z[i]` equals to `center + Z[center] - i`, which is actually the length of the part of the string at `j` within the range of `s[center - Z[center] : center + Z[center]]`. 

`Z[i]` cannot be any larger, or otherwise `s[center - Z[center] : center + Z[center]]` can also be expanded.


##### 3. `Z[j] = Z[center] + center - i` 

We can say for sure `Z[j] >= Z[i]` because its symmetric brother at `i` has a length of `Z[j]`. But we don't know what happens beyond `Z[center] + center`. So we need to do another *center expand approach* in this case.

## Code

```python
# Center expand
def countSubstrings1(self, S):
    N = len(S)
    ans = 0
    for center in xrange(2*N - 1):
        left = center / 2
        right = left + center % 2
        while left >= 0 and right < N and S[left] == S[right]:
            ans += 1
            left -= 1
            right += 1
    return ans

# Manacher algorithm in Python3
def countSubstrings2(self, S):
    A = '@#' + '#'.join(S) + '#$'
    Z = [0] * len(A)
    center = right = 0
    for i in xrange(1, len(A) - 1):
        if i < right:
            Z[i] = min(right - i, Z[2 * center - i])
        while A[i + Z[i] + 1] == A[i - Z[i] - 1]:
            Z[i] += 1
        if i + Z[i] > right:
            center, right = i, i + Z[i]
    return sum((v + 1) //2 for v in manachers(S))
```
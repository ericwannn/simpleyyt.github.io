---
layout: post
category: Programming
title: An introduction to bitset
tagline: by Snail
tags: 
  - C++
  - programming
published: true
---

# bitset
According to cpp official reference
> The class template bitset represents a fixed-size sequence of N bits. Bitsets can be manipulated by standard logic operators and converted to and from strings and integers. 

Bitset is a powerful tool dealing with ordered binary series, i.e. an array containing either 1 or 0.

## initialization

The way to initialize a bitset is similar to vector or map except that instead of data type, the length of the bitset is required to be declared when initialized. 
```cpp
bitset<32> bs;
```
This bitset is initialized with 32 bits length and all zeros. The elements can be visited with its index, from 0 to 31. The first bits are named low-order bits and last few bits are called high-order bits.

| methods  | description  |
|:--------:|:------------:|
| `bitset<N> bs;` | bs has N bits with 0s |
| `bitset<N> bs(u);` | Initialize with unsigned value |
| `bitset<N> bs(s);` | Initialize with string | 
| `bitset<N> bs(s, pos, n);` | Initialize with part of the string |


### Initialize with unsigned value

When initializing a bitset with unsigned value, the value is converted into binary first and read *from left to right*. 

If bitset has more bits than the binary value, the high-order bits are set to 0;  
If bitset had fewer bits than the binary value, the high-order bits of the value that do not fit into the bitset are thrown.  

For example, if we initialize a bitset with a unsinged long`0xffff` (16 ones and 16 zeros in binary) :

```cpp
bitset<16> b1(0xffff); // index 0~15: 1
bitset<32> b2(0xffff); // index 0~15: 1, 16~31: 0
bitset<64> b3(0xffff); // index 0~15: 1, 16~31: 0, 32~63: 0
```

### Initialize with string

When initializing a bitset with a string, the value is read *from right to left*.

For example,  if we initialize a bitset with string `1100`:

```cpp
bitset<16> b("1100"); // index 2 and 3: 1, others: 0
```

If we would like to use only part of the string, wo can do the following things:
```cpp
string s("1100001111");
bitset<16> bs(s, 7, 3); 
bitset<16> bs(s, s.size() - 2); // use last 2 bits of s to initialize bs
```

where parameters indicate that it uses the substring of `s` which starts from the `7`th element with a length of `3`. If `3` is not specified, it would use all the rest of the string by default.

## Member functions

Now we can play around with bitset! Here is a list of what we can do with a bitset.

| functions | description |
|:---------:|:-----------:|
| `bs.any()` | if any 1 exists in bs |
| `bs.none()`| if all bits are 0 |
| `bs.count()` | count the number of 1s in bs |
| `bs.size()` | return the number of bits in bs |
| `bs[pos]` | get the element in bs with index pos |
| `bs.test(pos)` | if element at pos is 1 | 
| `bs.set()` | set all bits to 1 |
| `bs.set(pos)`| set element at pos to 1 |
| `bs.reset()` | set all bits to 0 | 
| `bs.reset(pos)` | set element at pos to 0 |
| `bs.flip()` | change all 1s to 0 and all 0s to 1 |
| `bs.flip(pos)` | flip the element at pos |
| `bs.to_string()` | return a string representation of the data |
| `bs.to_ulong()` | return a `unsigned long integer` representation of the data |
| `bs.to_ullong()` | return a `unsigned long long integer` representation of the data |

## practice with problem

Bitset is useful in this LeetCode problem: 
 https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/description/

Find All Numbers Disappeared in an Array
Given an array of integers where 1 ≤ a[i] ≤ n (n = size of array),   
some elements appear twice and others appear once.  
Find all the elements of [1, n] inclusive that do not appear in this array.  
Could you do it without extra space and in O(n) runtime?   
You may assume the returned list does not count as extra space.  
```cpp
class Solution {
public:
    vector<int> findDisappearedNumbers(vector<int>& nums) {
        // Use bitset
        bitset<100000> bsq;
        for(int i = 0; i < nums.size(); ++i)
        {
            int idx = nums[i];
            bsq.set(idx);
        }
        vector<int> res;
        for(int i = 1; i <= nums.size(); ++i)
            if(!bsq.test(i))
                res.emplace_back(i);
        return res;
}; 
``` 
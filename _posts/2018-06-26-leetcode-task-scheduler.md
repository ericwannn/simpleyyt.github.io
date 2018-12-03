---
layout: post
category: Programming
title: Task scheduler - solution & explanation
tagline: by Snail
tags:
  - leetcode
  - programming
  - algorithm
published: true
---
# Task Scheduler

A very interesting [leetcode problem](https://leetcode.com/problems/task-scheduler/description/) came across to me today. The description is attached below. This problem could be solved in a smart and concise way so I would like to share with you: A 5 line C++ code that beats 100% other solutions

<!--more-->

## Problem description

> Given a char array representing tasks CPU need to do. It contains capital letters A to Z where different letters represent different tasks.Tasks could be done without original order. Each task could be done in one interval. For each interval, CPU could finish one task or just be idle.
>
>However, there is a non-negative cooling interval n that means between two same tasks, there must be at least n intervals that CPU are doing different tasks or just be idle.
>
>You need to return the least number of intervals the CPU will take to finish all the given tasks.
>
>Example 1:

```json
Input: tasks = ["A","A","A","B","B","B"], n = 2
Output: 8
Explanation: A -> B -> idle -> A -> B -> idle -> A -> B.
```

Note:
>
>The number of tasks is in the range [1, 10000].  
>The integer n is in the range [0, 100].

## Solution

This problem is solved with the following 5 lines C++ code in a really smart way.

```cpp
class Solution {
public:
    int leastInterval(vector<char>& tasks, int n) {
        unordered_map<char,int>mp;
        int fre = 0, ans = 0;
        for(auto e : tasks) fre = max(fre, ++mp[e]);
        for(auto e : mp) if(e.second == fre) ans++;
        return max((int)tasks.size(), ans + (fre-1)*(n+1));
    }
};
```

### Intuition

I will show you the main idea behind this algorithm. It's exactly the same whereas good for you to better understand this algorithm.

The idle interval is a problem when no task is cooled down and the CPU has nothing to process.

 If we had enough choices for tasks plus a relatively small cool down time, we will always be able to find a task ready to be executed so we don't need to worry about the cool down. But the minimum intervals required is the number of tasks we got.

In a simple case where cooling time is not a problem, the minimum required intervals is the number of tasks we got: `tasks.size()`.

In the relatively complicated case, we performs a slot filling strategy:

Given the frequency of the most frequent task is `f`,   
and the minimum cool down interval is `n`:

The working pipleline of CPU is like a queue where each position represents that if it's idle or processing any tasks.   
We seperate `f` tasks with a gap of size `n`, so we will have `f - 1` gaps and each gap has `n` empty slots. Since the most frequent task appears `f` times, other tasks appear at most `f - 1 ` times. Then we put the less frequent tasks into the these gaps at the same position i.e., the first of `n` slots. This guarantees the distance between these tasks is also `n`. We repeat this process untill we fill all tasks into this queue.

If there are still empty slots in the queue, it means no process can be done at that moment and the CPU needs to be idle. If we knew there is any idle time in this queue, the initial length of this queue is actually minimum intervals required.

I know you may ask: what if where isn't enough empty slots for the following less frequent tasks? In this situation, cooling time is actually no longer a problem because there is no idle time any more and we can easily insert these tasks into the queue. So in this case, again, the minimum required intervals is the number of tasks.

---

Let's take a look at an example:

> Input: tasks = AAABB, n = 2  
> Output: 7
> Explanation: A -> B -> idle -> A -> B -> idle -> A

`A` appears 3 times and `B` appears twice in this problem.

1. The first step is to put `A` into the queue with a gap of `n`:  
`A  _  _  A  _  _  A`

2. We put `B `into the empty slots:  
`A  B  _  A  B  _  A`

3. The remaining empty slots represent idle CPU time:  
`A  B  idle  A  B  idle  A`

The result is 7 !

---

Again, another example:

> Input: tasks = AACCCBEEE, n = 2  
> Output: 9
> Explanation: A -> B -> idle -> A -> B -> idle -> A

```
frequence count:
  C: 3
  E: 3
  A: 2
  B: 1
```

1. The first step is to put `C` and `E` into the queue, maintaining a gap of `n`:  
`C  E  _  C  E  _  C  E`

2. We put `A `into the empty slots:  
`C  E  A  C  E  A  C  E`

3. We have `B` to insert but there isn't empty slots left whichmeans that there is no idle time for CPU:   
The result will be the length of tasks list: 9 !

### Algorithm

The intuition is not very hard ! I hope you have understanded how it works. Now it comes to our really algorithm. I paste the code here with comment so you don't need to scoll back up.

It calculates exactly the same information mentioned aboved but in a slightly different way.

```cpp
class Solution {
public:
    int leastInterval(vector<char>& tasks, int n) {
        unordered_map<char,int>mp;
        int fre = 0, ans = 0;

        \\ Calculate the frequence of each task and get the largest one
        for(auto e : tasks) fre = max(fre, ++mp[e]);

        \\ Extract space required for trailing most frequent tasks
        \\ Go ahead if you are stuck here
        for(auto e : mp) if(e.second == fre) ans++;

        \\ Two cases:
        \\ 1. No idle time: It takes at least number of tasks intervals to process all tasks
        \\ 2. with idle time: calculate in the same way mentioned in previous chapter
        \\ ans +  (fre-1)*(n+1) represents the minimum space required
        return max((int)tasks.size(), ans + (fre-1)*(n+1));
    }
};
```

The question you might ask is that:  
why `ans + (fre-1)*(n+1)` is the minimum space required ? (`n + 1` is easy but why `fre - 1`?)    
why we need to consider trailing most frequent tasks?  

These two are actually one question: the way we calculate minimum required space (assume there is idle time)

Say we have `f` `A` tasks here and the cool down is `n`. The queue could be divided into `n` chunks, each of which has length `n + 1` because at least we need to ensure the distance between each two of task `A` is `n`.

| 0 | 1 | ... | n | n + 1 | ... |
|:-:|:-:|:---:|---|:-----:|:---:|
| A_1 | X | ... | X |   A_2   | ... |

`A_i` means the i-th `A` task we handle and X means empty slots. We will have `fre` chunks like this in order to put all `A` tasks. 

You may ask why `fre - 1`. Shouldn't it be `fre` ?

Two problem will occur if we simply do that:

1. since no other tasks are more frequent than `A`, so the there might be some empty slots at the tail of the queue after we put all tasks into this queue. Apparently we don't need to wait for these idle time after we have done everything.
2. All tasks that are less frequent than `A` can be inserted into the first `fre - 1` chunks, but what if there are some tasks that are equally frequent as `A`?

This is the motivation that we use `fre - 1` instead of `fre`.   
By using `(fre - 1) * (n + 1)`, we actually leave the last task of each most frequent task and we will have something like this, where `A` and `B` appear `fre` times in the task list and `C` is less frequent.

| 0 | 1 | 2 | ... | n | n + 1 | ... | (fre - 1) * (n + 1)  | (fre - 1) * ( n + 1)  + 1 | ... | fre * (n + 1) - 1 |
|:-:|:-:|:-:|:---:|:-:|:-----:|:---:|:--------------------:|:-------------------------:|:---:|:-----------------------:|
| A | B | C | ... | X |   A   | ... |           A          |             B             | ... |            X            |

Note that we have only `fre - 1` chunks here and `A` and `B` appear only `fre - 1` times. All other less frequent tasks can be inserted into this chunks (if there are intervals left as idle time), and what we have left ? The last task of each most frequent task. We kinda like appending these tasks to the queue, and this is why we calculate how many tasks are the most frequent task like this in order to take care of the trailing most frequent task.
```cpp
for(auto e : mp) if(e.second == fre) ans++;
```

Combine them together, that's `(fre - 1) * (n + 1)`, which contain the necessary space required for `fre - 1` most frequent tasks, plus the number of most frequent tasks, the space that one last task takes up. 

This is the principle of this algorithm. I hope you like it!

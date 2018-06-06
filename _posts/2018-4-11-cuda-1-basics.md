---
layout: post
category: Programming
title: CUDA kick-start
tagline: by Snail
tags: 
  - CUDA
  - programming
  - parallel programming
published: true
---

* How CUDA works

CUDA accelerates computations following a *single instruction multiple thread* (SIMT) parallelism pattern. 

CUDA GPU has multiple cores (threads) containing one ALU and one FPU (which are not important here). A streaming Multiprocessor (SM) has multiple cores.

By saying SIMT, CUDA breaks down a computation (one instruction) into several 


* CUDA API & C extension

* 
---
layout: post
category: Data engineering 
title: Intro to Pandas 
tagline: by Snail
tags: 
  - machine learning
  - data engineering
published: true
---

# Data pre-processing

* Threshold values into 0/1

```python
import pandas as pd

a = pd.Series([1,3,5,6,3,4,1,2,0,4])
b = Series(a>3).astype(int)
print(b)
```
---
title: "Machine Learning Project: Using Linear Regression to Predict Some Stock Prices of PETR4"
date: 2018-09-17
tags: [machine learning, data science, regression]
header:
  image: "/images/predictingstockprices/predicting.png"
excerpt: "Machine Learning, Regression, Data Science"
mathjax: "true"
---


This was my first project applying **Machine Learning** to do something, of course I won't get rich because of this algorithm, but it's so cool to see how the technology works.

**Some piece of code**
If you run my code, you'll see that the accuracy value it's something like 0.80~0.85, it's meaning that the algorithm can be assertive in 80%~85% of your predicting values.
```python
# This is an interesting value, this determines how much our algorithm can be "trusted" in percentage
accuracy = clf.score(X_test, y_test)
```



**Final work**
<img src="{{ site.url }}{{ site.baseurl }}/images/predictingstockprices/predicting2.png" alt="linearly separable data">

You can see the source code on: [link](https://github.com/mauricior/predictingstockprices)

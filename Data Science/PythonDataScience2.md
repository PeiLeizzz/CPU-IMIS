---
title: 基于 Python 的数据可视化
date: 2020-04-08 19:44:40
tags: 
- Data Science
- Python
categories:
- Data Science
toc: true
reward: true
---

<!--more-->

## 前置内容

> **Python 数据科学中常用的包**
>
> - 基础库
>
>   **Pandas**  **Numpy**  Scipy
>
> - 绘图及可视化
>
>   **Matplotlib  Seaborn**  Bokeh  Basemap  Plotly  NetworkX
>
> - 机器学习
>
>   **SciKit-Learn**  TensorFlow  Theano  Keras
>
> - 统计建模
>
>   **Statsmodels**
>
> - 自然语言处理、数据挖掘及其他
>
>   **NLTK**  Gensim  Scrapy  Pattern

## 数据及分析对象

数据集：`salaries`

要求：**理解数据，明确目的**

## 开始实战

### 1. 数据准备（加工）

由于 **`Seaborn`** 对数据的形态有要求，所以需要先将数据加工一下。

```Python
# 找到当前工作目录，将数据文件放入此目录中
import os
os.getcwd()
>>> 当前工作目录
```

```Python
# 读取数据
import pandas as pd
salaries = pd.read_csv('salaries.csv', index_col=0)
>>> index_col=0 意为数据表的第一行是列名
```

```Python
# 查看数据
salaries.head()
>>> 数据表的前五行
```

### 2. 导入 `Python` 包

```Python
# 导入 seaborn 包，并取别名为 sns
import seaborn as sns
```

### 3. 可视化绘图

```Python
# 设置图片样式(风格：darkgrid)
sns.set_style('darkgrid')
# 可以使用语句 sns.set_style?查看帮助

# 绘制散点图
sns.stripplot(data=salaries, x='rank', y='salary', jitter=True, alpha=0.5)
# jitter=True 代表允许抖动, alpha=0.5 代表透明度(点重叠时)

# 绘制箱线图
sns.boxplot(data=salaries, x='rank', y='salary')
```

![DS1.png](https://i.loli.net/2020/04/08/jOe1bMfQBitYRZ9.png)
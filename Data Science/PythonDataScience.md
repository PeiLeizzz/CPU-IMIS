---
title: 基于 Python 的统计建模(一)
date: 2020-04-07 21:19:50
tags: 
- Data Science
- Python
categories:
- Data Science
toc: true
reward: true
---

<!--more-->

$y = f(x)$

$x$: 自变量 为 **特征矩阵 行为样本，列为特征**
$y$: 因变量 为 **目标向量 $m$ 个标签对应 $m$ 个样本**

**步骤**：理解数据，确定目的

## 线性回归知识

**回归模型**: $y = β_0 + β_1x + E$

**回归方程**: $E(y) = β_0 + β_1x$ (或 $y=ax+b$)      
$β_0$：斜率   $β_1$：截距

- 计算的是 $y$ 的期望值：$E(y)$

- 用**样本统计量**来**估计总体参数**

**$β_0$** 的估计方法：**最小二乘法**等

1. 回归线的**拟合度优度评价**

   $R^2: [0, 1]$，越大越好

2. 残差项的非相关性评价 $Durbin-watson$ 值

3. 对回归分析的前提**假定**的检验是否确实存在线性关系：**$F$ 统计量 $p$ 值**$<0.05$ 或 $0.01$

4. 对回归分析的前提假定的检验**残差**项是否正态分布：**$JB$ 统计量 $p$ 值**$<0.05$ 或 $0.01$

## 实战

### 1. 数据的读入(`pandas`)

#### 查看当前工作目录

```python
import os
print(os.getcwd())

>>> 当前工作目录
```

#### 读入文件 `"women.csv"` 至 `Pandas` 数据框 `df_women`

```Python
import pandas as pd
df_women = pd.read_csv('women.csv', index_col=0)
	# 第二个参数表示：第 0 列为索引列/行名
print(df_women.head())

>>> height  weight
1      58     115
2      59     117
3      60     120
4      61     123
5      62     126
数据表前五行信息
```



**DataFrame**：类似于关系表（上面 `df_women` 就是由 `pd.read_csv` 形成的一张 `DataFrame` 表）

### 2. 数据的理解(`pandas`, `matplotlib`)

#### 查看数据形状（行数和列数）

```python
df_women.shape # shape 是个属性不是方法

>>> (15, 2)
(行数, 列数)
```

#### 查看数据框的简要信息

```python
df_women.info() # info()是方法

>>>Int64Index: 15 entries, 1 to 15
Data columns (total 2 columns):
height    15 non-null int64
weight    15 non-null int64
dtypes: int64(2)
memory usage: 360.0 bytes
简要信息
```

#### 查看列名

```python
print(df_women.columns)

>>> Index(['height', 'weight'], dtype='object')
index([列名 1, 列名 2], dtype='object') 
# Python 认为一切都是 object 类型
```

#### 查看描述性统计信息

```python
df_women.describe()

>>>height	weight
count	15.000000	15.000000
mean	65.000000	136.733333
std	4.472136	15.498694
min	58.000000	115.000000
25%	61.500000	124.500000
50%	65.000000	135.000000
75%	68.500000	148.000000
max	72.000000	164.000000 
描述性信息(count, mean, std(标准差), min, 25%, 50%, 75%, max 等信息)
```

#### 数据可视化

```Python
import matplotlib.pyplot as plt
%matplotlib inline  # 不另弹出对话框，在下面直接显示
# Python 中 @: 装饰器 %: 魔术命令(上面的是 jupyterNotebook 编辑器的命令)
plt.rcParams["font.family"] = 'Heiti TC'  # Mac
# windows 下
	# plt.rcParams['font.famliy'] = "SIMHEI" # 汉字显示
plt.scatter(df_women["height"], df_women["weight"]) 
	#散点图函数 参数 1：x 轴；参数 2：y 轴
plt.title('女性体重与身高数据的可视化')
plt.xlabel('身高')
plt.ylabel('体重')
plt.show()

>>> 散点图
```

![d1.png](https://i.loli.net/2020/04/15/ItPAQcZifw172qY.png)

### 3. 数据的规整化处理（数据的准备）

**自变量：特征矩阵** 

**因变量：目标向量**

方法：切片

#### 特征矩阵($x$)的生成

```python
x = df_women['height']
x

>>>
1     58
2     59
3     60
4     61
5     62
6     63
7     64
8     65
9     66
10    67
11    68
12    69
13    70
14    71
15    72
Name: height, dtype: int64 
        
height 列的数据
# 此时只是一个列，仍不是特征矩阵，但 statsmodels 可以自动转换
```

#### 目标向量($y$)的生成

```Python
y = df_women['weight']
y

>>>1     115
2     117
3     120
4     123
5     126
6     129
7     132
8     135
9     139
10    142
11    146
12    150
13    154
14    159
15    164
Name: weight, dtype: int64

weight 列的数据
# 此时已是目标向量
```

### 4. 模型的训练(`statsmodels`)

**注意点**：要训练截距必须要加一列 **`const`** 列

#### 特征矩阵的规整化处理

```python
import statsmodels.api as sm
x = sm.add_constant(x)
	# 给 x 新增一列，列名【const】，每行的取值均为 1.0
    # 用此方法的同时也是告诉计算机 y=ax+b 中的 b(截距)也计算(默认只计算 a)
x

>>>const	height
1	1.0	58
2	1.0	59
3	1.0	60
4	1.0	61
5	1.0	62
6	1.0	63
7	1.0	64
8	1.0	65
9	1.0	66
10	1.0	67
11	1.0	68
12	1.0	69
13	1.0	70
14	1.0	71
15	1.0	72 
const 和 height 两列组成的关系表
# 此时已满足[特征矩阵]形态的要求
```

#### 构建模型与模型拟合

```Python
# 构建模型
myModel = sm.OLS(y, x)  # OLS 是最小二乘法的缩写
	# 调用完此函数后 a 和 b 还未算出，只是把 x 和 y 交给模型

# 模型拟合
results = myModel.fit()
	# 此时 a 和 b 才计算完(基于数据拟合出参数)
```

### 5. 模型的解读与评价

#### 打印详细情况

```python
print(results.summary())

>>>OLS Regression Results                            
==============================================================================
Dep. Variable:                 weight   R-squared:                       0.991
Model:                            OLS   Adj. R-squared:                  0.990
Method:                 Least Squares   F-statistic:                     1433.
Date:                Wed, 15 Apr 2020   Prob (F-statistic):           1.09e-14
Time:                        10:14:20   Log-Likelihood:                -26.541
No. Observations:                  15   AIC:                             57.08
Df Residuals:                      13   BIC:                             58.50
Df Model:                           1                                         
Covariance Type:            nonrobust                                         
==============================================================================
                 coef    std err          t      P>|t|      [0.025      0.975]
------------------------------------------------------------------------------
const        -87.5167      5.937    -14.741      0.000    -100.343     -74.691
height         3.4500      0.091     37.855      0.000       3.253       3.647
==============================================================================
Omnibus:                        2.396   Durbin-Watson:                   0.315
Prob(Omnibus):                  0.302   Jarque-Bera (JB):                1.660
Skew:                           0.789   Prob(JB):                        0.436
Kurtosis:                       2.596   Cond. No.                         982.
============================================================================== 
把拟合后的详细情况打印出来
# 其中 a 是 height 的系数，b 是 const 的系数
# R-squared 是 R^2 的值， F-statistic 是 F 统计量，Prob(F-statistic)是 P 值
# Prob(JB)是 JB 的 P 值，Durbin-Watson 统计量
```

#### 单独打印各评价值

```Python
# 或单独调出各统计量
# 回归系数
results.params
# 残差
results.resid
# 残差的标准差
results.resid.std()
# 置信区间 (Confidence interval)
results.conf_int(alpha=0.025)
# R^2
print("rsquared=", results.rsquared)
# F 统计量的 P 值
results.f_pvalue
# DW 统计量
sm.stats.stattools.durbin_watson(results.resid)
# JB 统计量
sm.stats.stattools.jarque_bera(results.resid)
# 可视化预测结果
y_predict=results.predict()
	# result 值类似于 w=3.4*h-87 即 predict 是在已知 a、b、x 的情况下计算 y
y_predict

>>> array([112.58333333, 116.03333333, 119.48333333, 122.93333333,
       126.38333333, 129.83333333, 133.28333333, 136.73333333,
       140.18333333, 143.63333333, 147.08333333, 150.53333333,
       153.98333333, 157.43333333, 160.88333333]) 
输出预测的 weight 值
```

#### 线性回归分析可视化代码（和上面基本一样）

```python
plt.rcParams['font.family'] = 'Heiti TC'
# 或 plt.rcParams['font.family'] = 'SIMHEI'
plt.plot(df_women['height'], df_women['weight'], "o")
	# 原始数据，o 代表原点(散点)
plt.plot(df_women['height'], y_predict)
	# 预测的数据(线性拟合 y=ax+b)
plt.title('女性体重与身高的线性回归分析')
plt.xlabel('身高')
plt.ylabel('体重')

>>> 原始数据（散点图）和预测数据（线性）在一起的可视化图像
```

![d2.png](https://i.loli.net/2020/04/15/LAEgJG86B3XMIyW.png)

### 6. 模型的优化与重新选择（统计学方法）

**习题**：用多项式回归方法实现上面的例子

## 总结

回归拟合：**$y = f(x)$**

1. 根据自变量不同
   - 如果自变量最高次幂是一次——**简单回归**
   - 如果自变量最高次幂是两次或以上——**多项回归**
   - 如果自变量不止一个——**多元回归**
2. 根据因变量不同
   - 只有两种情况——**($0-1$)逻辑回归**
   - 计数性——泊松回归

**最后要进行模型假定的分析和假定**


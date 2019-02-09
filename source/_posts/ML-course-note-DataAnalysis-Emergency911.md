---
title: Emergency - 911 Calls 資料分析 
date: 2018-10-11 17:12:32
tags:
- Python
- Scikit-learn
---



> Dataset : [Kaggle - Emergency - 911 Calls / Montgomery County, PA](https://www.kaggle.com/mchirico/montcoalert)


## 使用Python套件
* 數據分析
    * NumPy
    * Pandas
* 資料視覺化
    * matplotlib
    * Seaborn

```python=
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```
<!-- more -->
## 讀取dataset

```python=
df = pd.read_csv('911.csv')
df.info() 
```
![](https://i.imgur.com/fQKB5ib.png)

```python=
df.head()
```
![](https://i.imgur.com/ceW88aL.png)

## 撥打911最多的5個郵遞區號

```python=
df['zip'].value_counts().head()
 
# 19401.0    6979
# 19464.0    6643
# 19403.0    4854
# 19446.0    4748
# 19406.0    3174
# Name: zip, dtype: int64
```

## 撥打911最多的5個鄉鎮
```python=
df['twp'].value_counts().head()

# LOWER MERION    8443
# ABINGTON        5977
# NORRISTOWN      5890
# UPPER MERION    5227
# CHELTENHAM      4575
# Name: twp, dtype: int64
```

## title欄有幾筆唯一的資料

```python=
# len(df['title'].unique()) 
df['title'].nunique()

# 110
```

## 分析title欄的類別，新增Reason欄位

```python=
#先取出類別
x = df['title'].iloc[0]
x.split(':')[0] # 'EMS'

# 新增Reasons欄位
df['Reason'] = df['title'].apply(lambda title: title.split(':')[0])
df
```
![](https://i.imgur.com/IXvkZaS.png)

## 各種Reason案件數量

```python=
df['Reason'].value_counts()

# EMS        48877
# Traffic    35695
# Fire       14920
# Name: Reason, dtype: int64
```

使用Seaborn產生圖表

```python=
sns.countplot(x = 'Reason', data = df, palette = 'viridis')
```

![](https://i.imgur.com/ZD3TXUI.png)

## 分析timeStamp，新增Hour、Month、Day of Week欄位

> 使用pandas的to_datetime轉成Date Time物件

```python=
type(df['timeStamp'].iloc[0]) # str

df['timeStamp'] = pd.to_datetime(df['timeStamp'])
type(df['timeStamp'].iloc[0]) # pandas._libs.tslibs.timestamps.Timestamp

time = df['timeStamp'].iloc[0]
time.hour # 取出hour值

# 新增欄位
df['Hour'] = df['timeStamp'].apply(lambda time: time.hour)
df['Month'] = df['timeStamp'].apply(lambda time: time.month)
df['Day of Week'] = df['timeStamp'].apply(lambda time: time.dayofweek)
```

重新mapping星期幾的欄位

```python=
dmap = {0:'Mon',1:'Tue',2:'Wed',3:'Thu',4:'Fri',5:'Sat',6:'Sun'}
df['Day of Week'] = df['Day of Week'].map(dmap)
df
```

![](https://i.imgur.com/7RFPfp2.png)

用Seaborn產生橫軸為**Day of Week**計量圖表

```python=
sns.countplot(x = 'Day of Week', data = df, hue = 'Reason', palette = 'viridis') 
plt.legend(bbox_to_anchor= (1.05, 1), loc = 2, borderaxespad = 0.)# 把legend放外面避免重疊
```
![](https://i.imgur.com/eKNApQ6.png)

**Month**為橫軸

```python=
sns.countplot(x = 'Month', data = df, hue = 'Reason', palette = 'viridis') 
plt.legend(bbox_to_anchor= (1.05, 1), loc = 2, borderaxespad = 0.)
```

![](https://i.imgur.com/PZ9rkgR.png)

## Day of Week對Hour的Heat map

> 需使用**groupby**與**unstack**重構dataFrame

```python=
dayHour = df.groupby(by = ['Day of Week', 'Hour']).count()['Reason'].unstack()
```

![](https://i.imgur.com/tS1xDVb.png)

用新的dataFrame產生Heat map

```python=
plt.figure(figsize=(12,6))
sns.heatmap(dayHour, cmap = 'viridis')
```

![](https://i.imgur.com/pfpk1Q2.png)
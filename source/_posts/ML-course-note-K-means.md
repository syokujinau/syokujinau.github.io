---
title: K-Means-Clustering演算法 - 腫瘤分類
date: 2018-10-12 17:16:19
tags:
- Machine Learning
- K-Means
---


> 非監督式機器學習演算法，本文將依照資料集的features來分類良性或惡性腫瘤(乳癌)
> 並用cufflinks作圖幫助我們做初步的資料分析
> dataset來自[scikit-learn](http://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_breast_cancer.html)


## 函式庫

```python=
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline

from plotly.offline import download_plotlyjs, init_notebook_mode, plot, iplot

import cufflinks as cf
# For Notebooks
init_notebook_mode(connected=True)
# For offline use
cf.go_offline()
```
<!-- more -->
## scikit-learn資料集

```python=
from sklearn.datasets import load_breast_cancer

data = load_breast_cancer()
data.keys()
# print(data.DESCR)

df = pd.DataFrame(data.data, columns= data.feature_names)
df['target'] = data.target

df.head()
```

![](https://i.imgur.com/25fKd74.png)

## cufflinks 互動式資料分析

```python=
df.iplot(kind='scatter',x='mean radius',y='mean texture',mode='markers',size=10, categories= 'target', xTitle='mean radius', yTitle='mean texture')
```

![](https://i.imgur.com/YPs3Wnb.png)

具有部分縮放功能可查看資料細節

![](https://i.imgur.com/kL71HaG.png)

 
## KMeans演算法

### 由scikit-learn的cluster引入

```python=
from sklearn.cluster import KMeans

kmeans = KMeans(n_clusters=2) #分成2類

kmeans.fit(df.drop('target', axis=1))  #注意! 非監督式不需要Label
```

### 查看重心

```python=
kmeans.cluster_centers_
```

array([[1.25562991e+01, 1.85703653e+01, 8.11234703e+01, 4.96061872e+02,
        9.48844977e-02, 9.10998174e-02, 6.24377642e-02, 3.34325434e-02,
        1.78057991e-01, 6.34540183e-02, 3.04190868e-01, 1.21515320e+00,
        2.15288059e+00, 2.37852922e+01, 7.17326256e-03, 2.34746895e-02,
        2.87455128e-02, 1.06363242e-02, 2.06135799e-02, 3.74750297e-03,
        1.40439018e+01, 2.47095434e+01, 9.19375114e+01, 6.19647945e+02,
        1.29959110e-01, 2.23311758e-01, 2.19214947e-01, 9.13298425e-02,
        2.83553653e-01, 8.32819406e-02],
       [1.93799237e+01, 2.16945802e+01, 1.28231298e+02, 1.18592977e+03,
        1.01294580e-01, 1.48612977e-01, 1.76939466e-01, 1.00698779e-01,
        1.91539695e-01, 6.06029008e-02, 7.42803817e-01, 1.22253817e+00,
        5.25058015e+00, 9.56781679e+01, 6.59868702e-03, 3.21766947e-02,
        4.24197710e-02, 1.56739847e-02, 2.03039695e-02, 3.95338931e-03,
        2.37094656e+01, 2.89126718e+01, 1.58496183e+02, 1.75302290e+03,
        1.40424733e-01, 3.57757710e-01, 4.49306107e-01, 1.92431069e-01,
        3.11881679e-01, 8.61654962e-02]])


### 分群結果

```python=
kmeans.labels_
```

array([0, 0, 0, 1, 0, 1, 0, 1, 1, 1, 1, 0, 0, 1, 1, 1, 1, 0, 0, 1, 1, 1,
       1, 0, 0, 0, 1, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1, 1, 1, 1, 1, 1, 0, 1,
       1, 0, 1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1,
       1, 1, 1, 1, 0, 1, 0, 1, 1, 0, 1, 0, 0, 1, 1, 1, 0, 0, 1, 0, 1, 0,
       1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 1,
       1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 1, 0, 0, 1, 1, 1, 1, 0, 1, 0, 1, 1,
       1, 1, 0, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,
       1, 1, 0, 1, 1, 1, 1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 1, 1, 1, 1, 1,
       1, 1, 1, 1, 0, 0, 0, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0,
       0, 1, 1, 0, 0, 1, 1, 1, 1, 0, 1, 1, 0, 1, 0, 1, 1, 1, 1, 1, 0, 0,
       1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 0, 1, 1, 0, 0, 1, 0, 1, 1,
       1, 1, 0, 1, 1, 1, 1, 1, 0, 1, 0, 0, 0, 1, 0, 1, 0, 1, 0, 0, 0, 1,
       0, 0, 1, 1, 1, 1, 1, 1, 0, 1, 0, 1, 1, 0, 1, 1, 0, 1, 0, 1, 1, 1,
       1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 1, 0, 1, 1, 1, 1, 1,
       1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 1, 0, 1, 1, 1, 1, 1, 1,
       1, 1, 1, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1,
       0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 1, 0, 0, 1, 1, 0, 0,
       1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 0, 0, 1, 1,
       1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1, 0,
       1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 1, 1, 1, 1, 1, 1,
       1, 0, 1, 1, 0, 1, 0, 1, 1, 0, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0,
       1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,
       1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 1, 1, 0, 0, 1, 1, 1, 0, 1, 1,
       1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1,
       1, 1, 1, 1, 1, 0, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,
       1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 0, 1, 0, 1])

### 視覺化比較分類結果

```python=
f, (ax1, ax2) = plt.subplots(1, 2, sharey=True,figsize=(10,5))
ax1.set_title('K Means')
ax1.scatter(df['mean radius'], df['mean texture'], c = kmeans.labels_)
ax2.set_title('Original')
ax2.scatter(df['mean radius'], df['mean texture'], c = df['target'])
```

![](https://i.imgur.com/rC33hjL.png)

很明顯良性惡性的結果label反了(因為n_clusters=2，所以類型非0即1)，所以修改預測數值 (此步驟不一定要做)

```python=
func = lambda x : 1 if x ==0 else 0
predictions = np.array([func(x) for x in kmeans.labels_])
```

再次作圖

```python=
f, (ax1, ax2) = plt.subplots(1, 2, sharey=True,figsize=(10,5))
ax1.set_title('K Means')
ax1.scatter(df['mean radius'], df['mean texture'], c = predictions)
ax2.set_title('Original')
ax2.scatter(df['mean radius'], df['mean texture'], c = df['target'])
```

![](https://i.imgur.com/2RVe9nG.png)


評估預測準確度

```python=
from sklearn.metrics import  classification_report, confusion_matrix
print(confusion_matrix(df['target'], predictions))

""" 
[[130  82]
 [  1 356]]
""" 
 
print(classification_report(df['target'], predictions))

"""
            precision    recall  f1-score   support

          0       0.99      0.61      0.76       212
          1       0.81      1.00      0.90       357

avg / total       0.88      0.85      0.84       569
"""
```
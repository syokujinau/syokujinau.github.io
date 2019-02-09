---
title: Linear Regression - Boston house-prices dataset  房價預測模型
date: 2018-10-11 17:10:42
tags:
- Machine Learning
- Linear Regression
---



> http://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_boston.html

## 引入Libraries

```python=
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```

## 讀取Dataset

```python=
from sklearn.datasets import load_boston
boston = load_boston()
print(boston.DESCR) #查看說明文件
```
- CRIM：城鎮人均犯罪率
- ZN：佔地面積超過25,000平方英尺的住宅用地比例。
- INDUS：每個城鎮的非零售業務面積比例
- CHAS：Charles River虛擬變量（如果管道限制河流則= 1;否則為0）
- NOX：一氧化氮濃度（每千萬份）
- RM：每棟住宅的平均房間數
- AGE：1940年以前建造的自住單位比例
- DIS：到波士頓五個就業中心的加權距離
- RAD：徑向高速公路的可達性指數
- TAX：每10,000美元的全額房產稅率
- PTRATIO：城鎮的學生與教師比例
- B：1000（Bk - 0.63）^ 2其中Bk是城鎮黑人的比例
- LSTAT：人口狀況下降％
- MEDV：自住房屋的中位數價值(單位: USD1000)
<!-- more -->
## 整理dataFrame

```python=
df = pd.DataFrame(boston.data.T, ['CRIM','ZN','INDUS','CHAS','NOX','RM' ,'AGE','DIS','RAD','TAX', 'PTRATIO','B','LSTAT']) #有13個feature
df = df.T
df['MEDV'] = boston.target.T  #MEDV即預測目標向量
df.head()
```

![](https://i.imgur.com/8oLJrgu.png)

## 查看各項統計數據


```python=
df.describe()
```

![](https://i.imgur.com/OKqyEuV.png)

## Exploratory data analysis 

```python=
sns.pairplot(df)
```


![](https://i.imgur.com/wzDcol2.jpg)


> 對單一變量的數值分布

```python=
sns.distplot(df['MEDV'])
```

![](https://i.imgur.com/RnHL0fW.png)

> 查看各種feature間的相關性
> 記得用corr()產生新的dataFrame

```python=
plt.figure(figsize=(18, 12))
sns.heatmap(df.corr(), annot= True, cmap = 'coolwarm')
```
![](https://i.imgur.com/Stfy0qC.png)

## 訓練線性回歸模型

### 載入sklearn

```python=
from sklearn.model_selection import train_test_split

X = df[['CRIM','ZN','INDUS','CHAS','NOX','RM' ,'AGE','DIS','RAD','TAX', 'PTRATIO','B','LSTAT']]
y = df['MEDV']
#分出30%的資料作為test set
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.3) 
```

### 使用LinearRegression建立並訓練模型

```python=
from sklearn.linear_model import LinearRegression
lm = LinearRegression()
lm.fit(X_train,y_train) #Fit linear model
```

> 到這裡就完成模型的訓練，接著看看訓練成果

## 模型評估

```python=
lm.coef_  # 查看各項係數
```

表列係數

```python=
coeff_df = pd.DataFrame(lm.coef_, ['CRIM','ZN','INDUS','CHAS','NOX','RM' ,'AGE','DIS','RAD','TAX', 'PTRATIO','B','LSTAT'], columns = ['Coefficient'])
coeff_df['註記'] = ['城鎮人均犯罪率','佔地面積超過25,000平方英尺的住宅用地比例','每個城鎮的非零售業務面積比例','Charles River虛擬變量（如果管道限制河流則= 1;否則為0）','一氧化氮濃度（每千萬份）','每棟住宅的平均房間數','1940年以前建造的自住單位比例','到波士頓五個就業中心的加權距離','徑向高速公路的可達性指數','每10,000美元的全額房產稅率','城鎮的學生與教師比例','1000*(Bk - 0.63)^2，其中Bk是城鎮黑人的比例','人口狀況下降百分比']
coeff_df
```

> coefficients代表feature每單位增加對房價(MEDV)的增量
> ex.平均房間數增加1，則房價(MEDV)會增加3.890080(單位:USD1000)

![](https://i.imgur.com/Wq7OZvj.png)

## 用建立的模型做預測

```python=
predictions = lm.predict(X_test)
```
predictions即為**預測房價**，真實房價為y_test
用scatter plot看預測正確性，高度正相關代表預測誤差越小


```python=
plt.scatter(y_test,predictions)   
plt.xlabel('Measured')
plt.ylabel('Predicted')
```

![](https://i.imgur.com/MrJAx1U.png)

用distribution plot檢查預測與實際的差值，residual = y_test-predictions
若residual位置集中在0的常態分布，則模型建立完成

```python=
sns.distplot((y_test-predictions), bins=50);  
```
![](https://i.imgur.com/mKlAZiF.png)


## Regression Evaluation Metrics

評估回歸模型
**Mean Absolute Error** (MAE) is the mean of the absolute value of the errors:

$$\frac 1n\sum_{i=1}^n|y_i-\hat{y}_i|$$

**Mean Squared Error** (MSE) is the mean of the squared errors:

$$\frac 1n\sum_{i=1}^n(y_i-\hat{y}_i)^2$$

**Root Mean Squared Error** (RMSE) is the square root of the mean of the squared errors:

$$\sqrt{\frac 1n\sum_{i=1}^n(y_i-\hat{y}_i)^2}$$

以上都是**損失函數**，越大代表與真實數據的不一致性越大，所以我們需要最小化他們才能做出正確預測

```python=
from sklearn import metrics
print('MAE:', metrics.mean_absolute_error(y_test, predictions))
print('MSE:', metrics.mean_squared_error(y_test, predictions))
print('RMSE:', np.sqrt(metrics.mean_squared_error(y_test, predictions)))

# MAE: 82288.22251914957
# MSE: 10460958907.209501
# RMSE: 102278.82922291153
```


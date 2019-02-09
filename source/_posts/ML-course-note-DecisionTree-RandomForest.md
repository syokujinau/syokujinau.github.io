---
title: Decision Tree and Random Forest - Lending Club 信貸資料分析
date: 2018-10-11 17:14:55
tags:
- Machine Learning
- Decision Tree
- Random Forest
---




> Dataset [Download](https://drive.google.com/file/d/1MSbXgZrlQkPBzBNkHPYidYHxPRn9uP1G/view?usp=sharing)
> 各column所代表的意義如下
 
* credit.policy: 1 if the customer meets the credit underwriting criteria of LendingClub.com, and 0 otherwise.
* purpose: The purpose of the loan (takes values "credit_card", "debt_consolidation", "educational", "major_purchase", "small_business", and "all_other").
* int.rate: The interest rate of the loan, as a proportion (a rate of 11% would be stored as 0.11). Borrowers judged by LendingClub.com to be more risky are assigned higher interest rates.
* installment: The monthly installments owed by the borrower if the loan is funded.
<!-- more -->
* log.annual.inc: The natural log of the self-reported annual income of the borrower.
* dti: The debt-to-income ratio of the borrower (amount of debt divided by annual income).
* fico: The FICO credit score of the borrower.
* days.with.cr.line: The number of days the borrower has had a credit line.
* revol.bal: The borrower's revolving balance (amount unpaid at the end of the credit card billing cycle).
* revol.util: The borrower's revolving line utilization rate (the amount of the credit line used relative to total credit available).
* inq.last.6mths: The borrower's number of inquiries by creditors in the last 6 months.
* delinq.2yrs: The number of times the borrower had been 30+ days past due on a payment in the past 2 years.
* pub.rec: The borrower's number of derogatory public records (bankruptcy filings, tax liens, or judgments).


## 函式庫


```python=
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```

## 引入資料集

```python=
df = pd.read_csv('loan_data.csv')
df.head()
```

![](https://i.imgur.com/yBHSpml.png)


```python=
df.info()
```

<class 'pandas.core.frame.DataFrame'>
RangeIndex: 9578 entries, 0 to 9577
Data columns (total 14 columns):
credit.policy        9578 non-null int64
purpose              9578 non-null object
int.rate             9578 non-null float64
installment          9578 non-null float64
log.annual.inc       9578 non-null float64
dti                  9578 non-null float64
fico                 9578 non-null int64
days.with.cr.line    9578 non-null float64
revol.bal            9578 non-null int64
revol.util           9578 non-null float64
inq.last.6mths       9578 non-null int64
delinq.2yrs          9578 non-null int64
pub.rec              9578 non-null int64
not.fully.paid       9578 non-null int64
dtypes: float64(6), int64(7), object(1)
memory usage: 1.0+ MB

```python=
df.describe()
```

![](https://i.imgur.com/7N890vM.png)


## 資料分析

個別顯示credit.policy為1跟0的fico值分布之histogram

```python=
plt.figure(figsize=(10, 6))
df[ df['credit.policy'] == 1 ]['fico'].hist(bins= 35, color = 'blue', label = 'Credit Policy = 1', alpha = 0.6)
df[ df['credit.policy'] == 0 ]['fico'].hist(bins= 35, color = 'red', label = 'Credit Policy = 0', alpha = 0.6)
plt.legend()
plt.title('FICO')
```

![](https://i.imgur.com/0411JUG.png)


同上，改成分為not.fully.paid為1、0的histogram

```python=
plt.figure(figsize=(10, 6))
df[ df['not.fully.paid'] == 1 ]['fico'].hist(bins= 35, color = 'blue', label = 'not.fully.paid = 1', alpha = 0.6)
df[ df['not.fully.paid'] == 0 ]['fico'].hist(bins= 35, color = 'red', label = 'not.fully.paid = 0', alpha = 0.6)
plt.legend()
plt.title('FICO')
```

![](https://i.imgur.com/8Flad85.png)

使用seaborn，產生countplot來顯示每種purpose對應的not.fully.paid為0、1的資訊

```python=
plt.figure(figsize=(11, 7))
sns.countplot(x = 'purpose', hue = 'not.fully.paid', data = df)
```

![](https://i.imgur.com/5JLTwFS.png)

顯示fico值與int.rate之間的關係
 
```python=
sns.jointplot(x = 'fico', y= 'int.rate', data = df)
```

![](https://i.imgur.com/jKjwOhi.png)

```python=
plt.figure(figsize=(11,7))
sns.lmplot(y= 'int.rate', x= 'fico', data = loans, hue = 'credit.policy', col = 'not.fully.paid', fit_reg=False)
```

![](https://i.imgur.com/xe152ig.png)


## 資料處理

> 將類別變數改成dummy variable


```python=
cat_feats = ['purpose']  #紀錄類別column名稱

final_data = pd.get_dummies(df, columns=cat_feats, drop_first=True)  #drop_first = True會丟棄第一行(但仍然能夠藉由其他行推斷類別)，減少不必要的資料
```

## 切分訓練、測試資料

```python=
from sklearn.model_selection import train_test_split

X = final_data.drop('not.fully.paid', axis = 1)
y = final_data['not.fully.paid']

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3)
```

## 訓練Decision Tree model

```python=
from sklearn.tree import DecisionTreeClassifier

dTree = DecisionTreeClassifier()
dTree.fit(X_train, y_train)
```

### 預測與評估

```python=
from sklearn.metrics import classification_report, confusion_matrix

predictions = dTree.predict(X_test)
print(confusion_matrix(y_test, predictions))

'''
[[2006  395]
 [ 357  116]]
'''

print(classification_report(y_test, predictions))

'''
             precision    recall  f1-score   support

          0       0.85      0.84      0.84      2401
          1       0.23      0.25      0.24       473

avg / total       0.75      0.74      0.74      2874
'''
```

## 訓練Random Forest model

```python=
from sklearn.ensemble import RandomForestClassifier

rfc = RandomForestClassifier(n_estimators= 300)
rfc.fit(X_train, y_train)
```

### 預測與評估

```python=
from sklearn.metrics import classification_report, confusion_matrix

predictions = dTree.predict(X_test)
print(confusion_matrix(y_test, predictions))

'''
[[2363   38]
 [ 447   26]]
'''

print(classification_report(y_test, predictions))

'''
             precision    recall  f1-score   support

          0       0.84      0.98      0.91      2401
          1       0.41      0.05      0.10       473

avg / total       0.77      0.83      0.77      2874
'''
```
---
title: Tensorflow Notebook 2 - Linear Regression
tags:
  - Tensorflow
  - Deep Learning
  - AI
  - Python
  - Jupyter
categories:
- Miscellaneous
thumbnail: 'https://i.imgur.com/zJX2v4b.jpg'
date: 2018-12-04 16:42:49
---
[![](https://i.imgur.com/BKsRpNc.png)](https://www.udemy.com/complete-guide-to-tensorflow-for-deep-learning-with-python/learn/v4/content)


## Setup

1. Download [Python 3.7 version ](https://www.anaconda.com/download/)
2. 勾選新增環境變數 (沒勾就要手動加，不然就開Anaconda Prompt下指令也可)
![]()
<img src="https://i.imgur.com/8U9bgby.png" width="70%">
3. cd到教學資料夾，有tfdl_env.yml檔案
4. 執行`conda env create -f tfdl_env.yml`
5. 執行`activate tfdeeplearning`
6. 執行`jupyter notebook`

## Basic Training Flow

基本流程
1. 建立Graph
2. 初始化Session
3. 丟入data並取得輸出



## Linear Regression

<!-- more -->
```python
import numpy as np
import tensorflow as tf
import matplotlib.pyplot as plt
%matplotlib inline
```


```python
x_data = np.linspace(0,10,10) + np.random.uniform(-1.5,1.5,10) # 加入雜訊
y_label = np.linspace(0,10,10) + np.random.uniform(-1.5,1.5,10)
```

### 模擬實際資料分布


```python
plt.plot(x_data,y_label,'o')
```


![](https://i.imgur.com/LD9dX5Y.png)



給定隨機斜率與截距


```python
m = tf.Variable(0.5) # 隨便給的variable ~
b = tf.Variable(0.5)
```

### Cost Function


如同吳恩達在[Linear Regression](https://medium.com/@ken90242/machine-learning%E5%AD%B8%E7%BF%92%E6%97%A5%E8%A8%98-coursera%E7%AF%87-linear-regression-with-one-variable-model-representation-cost-1c5ff295ac2c)所講的cost function
$$J(\theta_0, \theta_1) = \frac{ 1 }{ 2m } \displaystyle \sum_{ i = 1 }^{ m } (\hat{ y }^{(i)} - y^{(i)})^2 = \frac{ 1 }{ 2m } \displaystyle \sum_{ i = 1 }^{ m } (h_\theta(x^{(i)}) - y^{(i)})^2 $$

$\theta_0$是截距b，$\theta_1$是斜率m

$\hat{ y }$或是$h_\theta(x)$就是預測的$y$值
所以預測與現實的差距$(\hat{ y } - y)$就是誤差，而平方目的是punish，讓誤差大時累加error更大，並在誤差減少時快速遞減


```python
error = 0

for x,y in zip(x_data,y_label):
    
    y_hat = m*x + b  # hypothesis
    
    error += (y_hat - y)**2 
```

### 梯度下降法最小化Cost Function


```python
optimizer = tf.train.GradientDescentOptimizer(learning_rate=0.001) # learning_rate通常就用0.001
train = optimizer.minimize(error)
```

train存的是optimizer傳入cost function(error)回傳的operation，所以之後要丟入session.run()

### 初始化 Variables


```python
init = tf.global_variables_initializer()
```

###  Session


```python
with tf.Session() as sess:
    
    sess.run(init)
    
    epochs = 100  # 1個epoch表示跑過train set中所有樣本一次
    
    for i in range(epochs):
        sess.run(train)
        
    final_slope ,final_intercept = sess.run([m,b])
    
print('%d次epochs的線性回歸函數: y = %.2fx + %.2f ' % (epochs, final_slope, final_intercept))
# 100次epochs的線性回歸函數: y = 0.91x + 0.42 
```

    
    

### Result


```python
x_test = np.linspace(-1,11,10)
y_pred_plot = final_slope * x_test + final_intercept

plt.plot(x_test,y_pred_plot,'r')
plt.plot(x_data,y_label,'o')
```

![](https://i.imgur.com/9yZyLHH.png)




### 實驗1



```python
import numpy as np
import pandas as pd
import tensorflow as tf
import matplotlib.pyplot as plt

%matplotlib inline
```


```python
x_data = np.linspace(0.0, 10.0, 1000000)
 
noise = np.random.randn(len(x_data))
```

$y = wx + b + noise$


```python
y_true = (0.5 * x_data) + 5 + noise
```

```python
x_df = pd.DataFrame(data=x_data, columns=['X'])
y_df = pd.DataFrame(data=y_true, columns=['Y'])

my_data = pd.concat([x_df, y_df], axis=1)
my_data.head()



my_data.sample(n=250).plot(kind='scatter', x='X', y='Y')
```




![](https://i.imgur.com/mdirfvF.png)



```python
m = tf.Variable(0.5) # random numbers
b = tf.Variable(1.0)
```


```python
x_ph = tf.placeholder(tf.float32, [batch_size])
y_ph = tf.placeholder(tf.float32, [batch_size])
```

預測函數:
$\hat{ y } = m * x + b$

```python
y_hat = m * x_ph + b
```

使用tensorflow的API來描述cost function
```python
error = tf.reduce_sum(tf.square(y_hat - y_ph))
```

梯度下降法optimizer 

```python
optimizer = tf.train.GradientDescentOptimizer(learning_rate=0.001)
train = optimizer.minimize(error)
```

初始化graph中的global variables，回傳值是operation

```python
init = tf.global_variables_initializer()
```

**Session: 定義bach size**

```python
batch_size = 8
with tf.Session() as sess:
    sess.run(init)
    
    # 每一批丟入8個實際(x, y)值，optimizer會最佳化回歸直線，共做1000次
    batches = 1000
    for i in range(batches):
        rand_idx = np.random.randint(len(x_data), size = batch_size)  # 回傳隨機8個index值的陣列
        
        feed = {x_ph: x_data[rand_idx], y_ph: y_true[rand_idx]}
        
        sess.run(train, feed_dict=feed)
        
    final_slope ,final_intercept = sess.run([m,b]) # 記得m,b是上面宣告的tf.Variable()
```




```python
y_hat = final_slope * x_data + final_intercept
my_data.sample(250).plot(kind='scatter', x='X', y='Y')
plt.plot(x_data, y_hat, 'r')
```

最後training的結果(圖中紅線)

![](https://i.imgur.com/ZmMh2aV.png)



### 實驗2 - 使用estimator API



```python
import numpy as np
import pandas as pd
import tensorflow as tf
import matplotlib.pyplot as plt

%matplotlib inline
```

產生樣本  

```python
x_data = np.linspace(0.0, 10.0, 1000000)
```

加入雜訊

```python
noise = np.random.uniform(-2, 2, len(x_data))
```

$y = wx + b + noise$


```python
y_true = (0.8 * x_data) + 3 + noise
```

畫出模擬的資料分布

```python
myDataset = pd.concat([pd.DataFrame(data=x_data,columns=['X Data']),pd.DataFrame(data=y_true,columns=['Y'])],axis=1)
myDataset.sample(250).plot(kind='scatter',x='X Data',y='Y')
```
![](https://i.imgur.com/T4nNorC.png)



#### 使用estimator API來做訓練


```python
# 首先要設定feature類型、倒入哪種模型，可以想成格式設定
feat_cols = [tf.feature_column.numeric_column('x',shape=[1])]  
estimator = tf.estimator.LinearRegressor(feature_columns=feat_cols)
```

#### 切分訓練、測試資料


```python
from sklearn.model_selection import train_test_split

x_train, x_eval, y_train, y_eval = train_test_split(x_data,y_true,test_size=0.3, random_state = 101)
```

#### 丟入樣本，類似之前的feed_dict動作


```python
input_func = tf.estimator.inputs.numpy_input_fn({'x':x_train},y_train,batch_size=4,num_epochs=None,shuffle=True)

train_input_func = tf.estimator.inputs.numpy_input_fn({'x':x_train},y_train,batch_size=4,num_epochs=1000,shuffle=False) # 接受參數，輸出數據訓練數據
eval_input_func = tf.estimator.inputs.numpy_input_fn({'x':x_eval},y_eval,batch_size=4,num_epochs=1000,shuffle=False) # 接受參數，並輸出驗證數據和測試數據
```

Trains a model given training data input_fn


```python
estimator.train(input_fn=input_func,steps=1000)
```


#### Evaluation


```python
train_metrics = estimator.evaluate(input_fn=train_input_func,steps=1000)
eval_metrics = estimator.evaluate(input_fn=eval_input_func,steps=1000)
```

    


```python
print("train metrics: {}".format(train_metrics))
print("eval metrics: {}".format(eval_metrics))
#train metrics: {'average_loss': 1.393801, 'global_step': 1000, 'loss': 5.5752039}
#eval metrics: {'average_loss': 1.3947791, 'global_step': 1000, 'loss': 5.5791163}
```

    
    

### Predictions


```python
# 假設我們得到一組新的樣本 np.linspace(0, 10, 10)
input_fn_predict = tf.estimator.inputs.numpy_input_fn({'x':np.linspace(0,10,10)},shuffle=False)


list(estimator.predict(input_fn=input_fn_predict))

# fetch results
predictions = []
for x in estimator.predict(input_fn=input_fn_predict):
    predictions.append(x['predictions'])

predictions


myPredict = pd.concat([pd.DataFrame(data=np.linspace(0,10,10),columns=['New X']), pd.DataFrame(data=predictions,columns=['predictions'])],axis=1)
print('my prediction line: y = %.2fx + %.2f' % ((myPredict.iloc[1]['predictions'] - myPredict.iloc[0]['predictions'])/((myPredict.iloc[1]['New X'] - myPredict.iloc[0]['New X'])), myPredict.iloc[0]['predictions']))
```

預測函數: $y = 0.81x + 2.75$
    


```python
myDataset.sample(n=250).plot(kind='scatter',x='X Data',y='Y')
plt.plot(np.linspace(0,10,10), predictions, 'r')
```


![](https://i.imgur.com/n0N6ZHo.png)

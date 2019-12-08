---
title: Tensorflow Notebook 1 - Python實作二元線性分類器
date: 2018-12-01 20:46:49
tags:
- Tensorflow
- Deep Learning
- AI
- Python
- Jupyter
categories:
- Miscellaneous
thumbnail: https://i.imgur.com/zJX2v4b.jpg
---



<!-- more -->
## Basic - Perceptron

生物的神經元，包含樹突(Dendrites)、細胞核(Body)、軸突(Axon)
![](https://i.imgur.com/V0TrxkI.png)

Artificial Neural Network模仿神經元運作

![](https://i.imgur.com/bwe5HDN.png)

> 令activation function運算每個input*weight之和
> 若weight皆為0，加入bias避免輸出恆為0的情況 

數學式:
$$
\sum_{i=0}^{n} w_{i}x_{i} + b
$$

### Multiple Perceptron Network

* Input layer 
    * real valus from the data

* Hidden laayer 
    * layers in between input and output
    * 3 or more layers is deep network
* Output layer
    * Final estimate of the output
 
![](https://i.imgur.com/j0WrsTc.png)

### Cost Function 

> **Notation** 
> y : true value
> a : neuron's prediction

#### 1. Quadratic Cost

$$
C = \frac{\sum (y-a)^{2}}{n}   
$$

y - a即為error越大的error，則cost越大
會造成learning speed下降

#### 2. Cross Entropy

$$
C =  \frac{-1}{n}\sum (ylna+(1-y)ln(1-a))
$$

error越大則learning speed越快
(the larger the difference, the faster the neuron can learn.)

### Concept

We need to figure out how we can use our **neurons(activation function)** and **the measurement of error (cost function)** and then attempt to **correct our prediction (learning)**

## Manual Neural Network

以下用python實作一個ANN，並分類一個2個label的資料集(二元線性分類器)

### Basic Graph

![](https://i.imgur.com/Ls9vU9U.png)

"Graph"可想像成一連串nodes，n1、n2為常數輸入，n3為"operation"，繼承此class實作各種運算，例如上圖add operation，最後輸出為3

### Python Implementation

#### Opeartion
```python=
class Operation():
    def __init__(self, input_nodes=[]):
        self.input_nodes = input_nodes
        self.output_nodes = []
        
        for node in input_nodes:
            node.output_nodes.append(self)
            
        _default_graph.operations.append(self)
            
    def compute(self):
        pass
```

```python=
class add(Operation):
    def __init__(self, x, y):
        super().__init__([x, y])
    def compute(self, val_x, val_y): #overwrite
        self.inputs = [val_x, val_y]
        return val_x + val_y
```

```python=
class multiply(Operation):
    def __init__(self, x, y):
        super().__init__([x, y])
    def compute(self, val_x, val_y): #overwrite
        self.inputs = [val_x, val_y]
        return val_x * val_y
```

```python=
class matmul(Operation): # matrix multiply
    def __init__(self, x, y):
        super().__init__([x, y])
    def compute(self, val_x, val_y): #overwrite
        self.inputs = [val_x, val_y]
        return val_x.dot(val_y)
```

#### Placeholder
```python=
class Placeholder():
    def __init__(self):
        self.output_nodes = [];
        _default_graph.placeholders.append(self)
```

#### Variable

```python=
class Variable():
    def __init__(self, initial_value = None):
        self.value= initial_value
        self.output_nodes = []
    
        _default_graph.variables.append(self)
```

#### Graph
```python=
class Graph():
    def __init__(self):
        self.operations = []
        self.placeholders = []
        self.variables = []
        
    def set_as_default(self):
        global _default_graph
        _default_graph = self
```

```
z = Ax + b
A = 10, b = 1
```

#### Session

此時需要先有post-order traverse function來查看運算是否按照正確順序，比如說這個範例需要先相乘再相加，因此定義session class


```python=
def traverse_postorder(operation):
    nodes_postorder = []
    def recurse(node):
        if isinstance(node, Operation):
            for input_node in node.input_nodes:
                recurse(input_node)
        nodes_postorder.append(node)
    
    recurse(operation)
    return nodes_postorder
```

```python=
class Session():   
    def run(self, operation, feed_dict={}): # feed_dict to input a batch of data
    
        nodes_postorder = traverse_postorder(operation)
        
        for node in nodes_postorder:
            
            if type(node) == Placeholder:
                node.output = feed_dict[node]
                
            elif type(node) == Variable:
                node.output = node.value
                
            else: #operation
                node.inputs = [input_node.output for input_node in node.input_nodes]
                node.output = node.compute(*node.inputs) # args
                
            if type(node.output) == list:
                node.output = np.array(node.output)
                
        return operation.output        
```
### Usage
```python=
# example1
g = Graph()
g.set_as_default()

A = Variable(10)
b = Variable(1)
x = Placeholder()

z = add(multiply(A, x), b)

sess = Session()

result = sess.run(operation = z, feed_dict = {x:10})
print(result) #101
```

```python=
# example2
g = Graph()
g.set_as_default()

# z = Ax + b
A = Variable([[10, 20], [30, 40]])
b = Variable([1, 2])

x = Placeholder()

y = matmul(A, x)
z = add(y, b)

sess = Session()
sess.run(operation=z, feed_dict={x:10})
```

### Perceptron type model

應用此模型來做分類，從簡單的linear classification開始!

#### Activation function

先引入視覺化套件
```python=
import matplotlib.pyplot as plt
%matplotlib inline
```

定義activation function
```python=
def sigmoid(z):
    return 1 / (1 + np.exp(-z))
```

先看看這個function的長相

```python=
sample_z = np.linspace(-10, 10, 100)
sample_a = sigmoid(sample_z)

plt.plot(sample_z, sample_a)
```

![](https://i.imgur.com/l5lVfc4.png)


建立運算(繼承Operation)

```python=
class Sigmoid(Operation):
    def __init__(self, z):
        super().__init__([z])
    def compute(self, z_val):
        return 1 / (1 + np.exp(-z_val))
```

#### Feature之間的線性關係

上面我們定義了神經元中的activation function，現在我們要找出feature之間的線性關係，這是我們從資料集中觀察到的結果，是人類自己需要歸納、分析的部分。
![](https://i.imgur.com/1S0XLxV.png)
(source: https://isaacchanghau.github.io/post/activation_functions/)

使用scikit-learn的內建資料集產生器

```python=
from sklearn.datasets import make_blobs

features, labels = make_blobs(n_samples = 50, n_features = 2, centers = 2, random_state = 55)
```

畫出斑點分布
```python=
plt.scatter(features[:, 0], features[:, 1], c=labels)
```

**注意!! 這張圖的兩軸各代表一種feature，顏色代表label**

![](https://i.imgur.com/FqgzriI.png)



現在，我可以用肉眼觀察，`y = x + 5`可以很好地劃分2種斑點
```python=
x1 = np.linspace(0, 11, 10)
y1 = x1 + 5
plt.scatter(features[:, 0], features[:, 1], c=labels)
plt.plot(x1, y1)
```

![](https://i.imgur.com/HPujnEy.png)



接著要實際定義perceptron來構成最簡單的前饋神經網路(Feedforward Neural Network)

> 前饋神經網路是最早被發明的ANN
> The feedforward neural network was the first and simplest type of artificial neural network devised. In this network, the information moves in only one direction, forward, from the input nodes, through the hidden nodes (if any) and to the output nodes. There are no cycles or loops in the network. - [Wikipedia](https://en.wikipedia.org/wiki/Feedforward_neural_network)

##### Matrix representation


`y = x + 5`其中x,y都是**feature**
可寫成`feat2 = feat1 + 5`，移項後為`-feat1 + feat2 - 5 = 0`
矩陣表示成 
<!-- $$
\begin{bmatrix}-1&1 \end{bmatrix} \begin{bmatrix} feat1 \\ feat2 \end{bmatrix} - 5 = 0 
$$ -->
![](https://i.imgur.com/eaHVvcJ.png)
所以給定feat1、feat2，代入上面式子為0代表在`y = x + 5`線上
令$f(feat1, feat2) = -feat1 + feat2 - 5$
若$f(feat1, feat2) > 0$表示在直線`y = x + 5`劃分的上半區域，即label1
若$f(feat1, feat2) < 0$表示在直線`y = x + 5`劃分的下半區域，即label2
> 因此稱之為linear classification


#### 建構神經元

```python=
g = Graph()
g.set_as_default()
```

$\sum_{i} wixi + b$
```python=
x = Placeholder()
# f(feat1, feat2) = −feat1 + feat2 − 5 
w = Variable([-1, 1])
b = Variable(-5)
```
activation function:
$a = f(\sum_{i} wixi + b )$
```python=
z = add(matmul(w, x), b)
a = Sigmoid(z)
```

![](https://i.imgur.com/Ucs8CnC.png)

再複習一次，sigmoid會給我們一個近似0或1的數，類比成神經元的行為就是當輸入訊號到達某個threshold(上圖就是0)，產生電脈衝。電脈衝沿著軸突並通過突觸傳遞到其它神經元


現在我們已經完成神經元的構造，現在我有一組features=(-8, 10)
丟進這個perceptron model，看看這個model會辨識出什麼
```python=
sess = Session()
sess.run(operation=a, feed_dict={x: [-8, 10]})
# 0.99999773967570205
```
輸出一個很接近1的數，代表label = 1
我們可以看到分布圖中(-8, 10)的位置就是直線上半的某點

```python=
sess.run(operation=a, feed_dict={x: [0, -5]})
# 4.5397868702434395e-05
```
輸出一個很接近0的數，代表label = 0

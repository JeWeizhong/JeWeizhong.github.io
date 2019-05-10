---
title: 机器学习笔记-----Logistic回归
categories: 
  - 机器学习
tags:
  - 机器学习
  - python3
  
date: 2019-01-23
mathjax: true

---

Logistic回归,有翻译为“逻辑回归”，但是它跟“逻辑”这个单词一点关系都没有，也不是解决回归问题的，是用来解决二分类的算法，最准确的翻译应该是“对数几率回归”

至于为什么叫对数几率刚回归，请看下面的推倒过程

##  线性回归回顾

先来看一个简单的线性回归模型：


$$ f(x) = \vec\omega^T \vec{x}+b  $$
这里的
$$\vec\omega^T = [\omega_1,\omega_2,\cdots,\omega_n]$$

$$\vec x  = \left[
\begin{matrix}
x_1 \\
x_2  \\
\cdots\\
x_n  
\end{matrix} \right]$$

一般来说给定的数据集是：$D=\{(\vec x^{(1)},y^{(1)}),(\vec x^{(2)},y^{(2)}),⋯,(\vec x^{(m)},y^{(m)})\}$, 即有m组数据，n个未知数，因此多变量线性回归的公式是：

$$f(x)= X^T \vec\omega $$

其中：
$$X = \left[
\begin{matrix}
x_1^{(1)} \   x_2^{(1)} \ x_1^{(1)} \ \cdots \ x_n^{(1)} \ 1 \\
x_1^{(2)} \   x_2^{(2)} \ x_3^{(2)} \ \cdots \ x_n^{(2)} \ 1 \\
x_1^{(3)} \   x_2^{(3)} \ x_3^{(3)} \ \cdots \ x_n^{(3)} \ 1 \\
\cdots \\
x_1^{(m)} \   x_2^{(m)} \ x_3^{(m)} \ \cdots \ x_n^{(m)} \ 1 
\end{matrix} \right] =  \left[
\begin{matrix}
\vec x^{(1)T} \ 1 \\
\vec x^{(2)T} \ 1 \\
\vec x^{(3)T} \ 1 \\
\cdots \\
\vec x^{(m)T} \ 1 \\
\end{matrix} \right]$$


展开式：

$$\omega_1 x_1^{(1)} + \omega_2 x_2^{(1)} + \cdots + \omega_n x_n^{(1)} +b = y^{(1)} $$

$$\omega_1 x_1^{(2)} + \omega_2 x_2^{(2)} + \cdots + \omega_n x_n^{(2)} + b = y^{(2)}$$

$$\cdots$$

$$\omega_1 x_1^{(m)} + \omega_2 x_2^{(m)} + \cdots + \omega_n x_n^{(m)} +b = y^{(m)}$$


我们为了便于推导，忽略了下标，只用了一组$(\vec{x},y)$,然后把$b$当做$\omega_0$，而此时$x_0 = 1$：

$$f(x)=\vec\omega^T \vec{x} $$

## sigmoid函数


线性回归很显然是一个连续的值，既然要解决二分类的问题，那最好是换成一个$0/1$的值

这里引出一个sigmoid函数：
$$g(z) = \frac {1}{1+e^{-z}}$$

![logtic](/myphoto/logtic.png)

令：

$$z =\vec\omega^T \vec{x}+b $$

$$f(x) = g(z) = \frac {1}{1+e^{-\vec\omega^T \vec{x}}} $$

我们就引出了我们的模型：

$$ln \frac{y}{1-y} =  \vec\omega^T \vec{x}+b $$

上式中$\frac{y}{1-y}$ 称为几率，反应x获得正例(y)的相对可能性，由此可以看出，模型实际上是在用线性回归模型的预测结果去逼近真实值$y$的对数几率，因此该模型就称为**对数几率回归模型**

## 代价函数

由于$y$的值为0,1，我们将$y$视为后验概率：

$$p(y=1 |  x;\omega) = f(x) $$

$$p(y=0 |  x;\omega) = 1- f(x) $$

$f(x)$越大，代表着预测值与$y$越接近，误差就越小，带入到公式中：

$$p(y=1 |  x;\omega) = \frac {e^{\vec\omega^T \vec{x}}}{1+e^{\vec\omega^T \vec{x}}} $$

$$p(y=0 |  x;\omega) = \frac {1}{1+e^{\vec\omega^T \vec{x}}}  $$


为了确定$\omega$用对数似然函数：

$$ \ell(\omega) = lnp(y|x ;\omega)$$

可以合并到一起：

$$ p(y|x ;\omega) = f(x)^y(1-f(x))^{(1-y)}$$

带入到对数似然函数中：

$$ln p(y|x ;\omega) = y lnf(x) + (1-y)ln(1-f(x)) $$

我们得到的代价函数：

$$J(\omega) = - \frac{1}{m} \sum_{i=1}^m [ y^{(i)} lnf(x^{(i)}) + (1-y^{(i)})ln(1-f(x^{(i)})) ] $$

$J(\omega)$越小，就说明$f(x) = 1$的概率越大，因此只要求出$J(\omega)$的**最小值**


>ps：
>
>1）其实正常推倒出来的J(w)是没有前面的负号的，这里为了习惯，用梯度下降法求最小值，所以加了一个负号
>
>2） 之前推倒时候忽略了上标，但是我们在最后的损失函数中为了完整性就把上标加了上去


## 梯度下降法


对代价函数求导的过程就不写了，直接写最后的结果:

$$\frac{\partial J(\omega)}{\partial\omega_j} =\frac{1}{m} \sum_{i=1}^m (f(x^{(i)}) - y^{(i)})x_j^{(i)}  $$

$$\omega_{j+1} := \omega_j- \frac{1}{m}\alpha\sum_{i=1}^m(f(x^{(i)}) - y^{(i)})x_j^{(i)} $$

## sklearn代码实现逻辑回归






```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
```


```python
dataset = pd.read_csv(r'E:\Downloads\100-Days-Of-ML-Code-master\Social_Network_Ads.csv')
dataset.head(5)
```

```python
X = dataset.iloc[:,[2,3]].values
Y = dataset.iloc[:,4].values
```


```python
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, Y, test_size = 0.25, random_state = 0)
```


```python
from sklearn.preprocessing import StandardScaler
sc = StandardScaler()
X_train = sc.fit_transform(X_train)
X_test = sc.transform(X_test)
```    

```python
from sklearn.linear_model import LogisticRegression
classifier = LogisticRegression()
classifier.fit(X_train, y_train)
# classifier.coef_
```

```python
y_pred = classifier.predict(X_test)
```


```python
from sklearn.metrics import confusion_matrix
cm = confusion_matrix(y_test, y_pred)
```
可视化

```python
from matplotlib.colors import ListedColormap
X_set,y_set=X_train,y_train
X1,X2=np. meshgrid(np. arange(start=X_set[:,0].min()-1, stop=X_set[:, 0].max()+1, step=0.01),
                   np. arange(start=X_set[:,1].min()-1, stop=X_set[:,1].max()+1, step=0.01))
plt.contourf(X1, X2, classifier.predict(np.array([X1.ravel(),X2.ravel()]).T).reshape(X1.shape),
             alpha = 0.75, cmap = ListedColormap(('red', 'green')))
plt.xlim(X1.min(),X1.max())
plt.ylim(X2.min(),X2.max())
for i,j in enumerate(np. unique(y_set)):
    plt.scatter(X_set[y_set==j,0],X_set[y_set==j,1],
                c = ListedColormap(('red', 'green'))(i), label=j)

plt. title(' LOGISTIC(Training set)')
plt. xlabel(' Age')
plt. ylabel(' Estimated Salary')
plt. legend()
plt. show()

X_set,y_set=X_test,y_test
X1,X2=np. meshgrid(np. arange(start=X_set[:,0].min()-1, stop=X_set[:, 0].max()+1, step=0.01),
                   np. arange(start=X_set[:,1].min()-1, stop=X_set[:,1].max()+1, step=0.01))

plt.contourf(X1, X2, classifier.predict(np.array([X1.ravel(),X2.ravel()]).T).reshape(X1.shape),
             alpha = 0.75, cmap = ListedColormap(('red', 'green')))
plt.xlim(X1.min(),X1.max())
plt.ylim(X2.min(),X2.max())
for i,j in enumerate(np. unique(y_set)):
    plt.scatter(X_set[y_set==j,0],X_set[y_set==j,1],
                c = ListedColormap(('red', 'green'))(i), label=j)

plt. title(' LOGISTIC(Test set)')
plt. xlabel(' Age')
plt. ylabel(' Estimated Salary')
plt. legend()
plt. show()
```

![png](/myphoto/output_12_0.png)


![png](/myphoto/output_12_1.png)


---
title: Python实现分类算法
date: 2017-10-02 09:34:57
tags: [Python,机器学习]
---

用 Python 实现两种简单的神经网络算法：感知器分类算法，线性神经元分类算法
<!-- more -->

分类算法的描述：使用 Pandas， Nympy 和 matplotlib 等 Python 开发库去读取加工和可视化数据集。

<center>
<img src="./1.png" style="width:300px;"><span> </span><img src="./2.png" style="width:300px;">
</center>

## 感知器分类算法
步骤: 

权重向量 W,训练样本 X

1. 把权重向量初始化为 0,或把每个分量初始化为[0,1]间任意小数
2. 把训练样本输入感知器，得到分类结果(-1 或 1）
3. 根据分类结果跟新权重值向量


步调函数与阈值
<center>
<img src="./a1.png" style="width:300px;">
</center>

权重更新算法
<center>
<img src="./a2.png" style="width:300px;">
</center>

阀值的更新
<center>
<img src="./a3.png" style="width:300px;">
</center>

感知算法的适用范围：线性可分割
<center>
<img src="./a4.png" style="width:400px;">
</center>

感知器分类算法
<center>
<img src="./a5.png" style="width:400px;">
</center>

编码实现

	```py
	# -*- coding: UTF-8 -*-
	# 感知器分类算法
	import numpy as np
	import pandas as pd
	import matplotlib.pyplot as plt
	class Perceptron(object):
	    # eta:学习率  n_iter： 权重向量训练的次数  w_：神经分叉权重向量
	    # errors_：用于记录神经元判断出错的次数
	    def __init__(self, eta = 0.01, n_iter = 10):
	        self.eta = eta
	        self.n_iter = n_iter
	        pass
	    # 做一个点积运算   W × X
	    def net_input(self, X):
	        #z=w0*1+w1*x1+...+wn*xn
	        return np.dot(X, self.w_[1:]) + self.w_[0]
	        pass
	    def predict(self, X):
	        return np.where(self.net_input(X) >= 0.0, 1, -1)
	        pass
	    # 输入训练数据，培训神经元
	    # x 输入样本向量， y对应的样本分类
	    # x shape[n_samples, n_features] x:[[1, 2, 3], [4, 5, 6]]
	    # 2 3 x.shape[1] = 2; x.shape[2] = 3
	    def fit(self, X, y):
	        # 初始化权重为0 加一是因为步调函数阈值
	        self.w_ = np.zeros(1 + X.shape[1])
	        self.errors_ = []
	        # print self.w_
	        for _ in range(self.n_iter):
	            errors = 0
	            ## print _
	            for xi, target in zip(X, y):
	                # update = v * (y - y')
	                update = self.eta * (target - self.predict(xi))
	                ## print 'predict:', self.predict(xi)
	                ## print 'update:', update
	                # xi是一个向量  update * xi 等价：
	                # w(1)' = x[1] *update , w(2)' = x[2] *update , w(3)' = x[3] *update
	                ## print 'XI :', xi
	                self.w_[1:] += update * xi
	                self.w_[0] += update
	                errors += int(update != 0.0)
	                self.errors_.append(errors)
	                ## print self.w_[1:]
	                pass
	            pass
	        print "WEight:", self.w_
	        pass
	    pass
	from matplotlib.colors import ListedColormap
	def plot_decision_regions(x, y, classifier, resolution = 0.02):
	    marker = ('s', 'x', 'o', 'v')
	    colors = ('red', 'blue', 'green', 'gray', 'gray', 'cyan')
	    cmap = ListedColormap(colors[:len(np.unique(y))])
	    x1_min, x1_max = x[: , 0].min() - 1, x[: , 0].max()
	    x2_min, x2_max = x[: , 1].min() - 1, x[: , 1].max()
	    print x1_min, x1_max, x2_min, x2_max
	    # 185 从3.3- 6.98 每隔0.02
	    # 255 从0  - 5.08 每隔0.02
	    # xx1   从3.3 - 6.98 为一行  有185行相同的数据
	    # xx2   从0   - 5.08 为一列  第一行全为0 第二行全1 (255, 185)
	    xx1, xx2 = np.meshgrid(np.arange(x1_min, x1_max, resolution),
	                           np.arange(x2_min, x2_max, resolution))
	    # print np.arange(x1_min, x1_max, resolution).shape
	    # print np.arange(x1_min, x1_max, resolution)
	    # print np.arange(x2_min, x2_max, resolution).shape
	    # print np.arange(x2_min, x2_max, resolution)
	    # print xx2.shape
	    # print xx2
	    # 相当于 np.arange(x1_min, x1_max, resolution) np.arange(x2_min, x2_max, resolution)
	    # 已经在分类了站如果是3.3 0 则为1 6.94 5.08 则-1
	    z = classifier.predict(np.array([xx1.ravel(), xx2.ravel()]).T)
	    print xx1.ravel()
	    print xx2.ravel()
	    print z
	    z = z.reshape(xx1.shape)
	    print z
	    # 在两个分类之间画分界线
	    plt.contourf(xx1, xx2, z, alpha = 0.4, cmap = cmap)
	    plt.xlim(x1_min, x1_max)
	    print "xx1.min()", x1_min
	    plt.ylim(xx2.min(), xx2.max())
	    plt.xlabel('length of the huaban')
	    plt.ylabel('length of the huajing')
	    plt.legend(loc='upper left')
	    plt.show()
	def main():
	    ## X = np.array([[1, 2, 3], [4, 5, 6]])
	    # print X.shape
	    ## y = [1, -1]
	    df = pd.read_csv('iris.csv', header = None)
	    ## print df.head(10)
	    y = df.loc[0:100, 4].values
	    y = np.where(y == 'Iris-setosa', 1, -1)
	    x = df.iloc[0:100, [0, 2]].values
	    print x.shape
	    print y.shape
	    plt.scatter(x[:50, 0], x[:50, 1], color = 'red', marker = 'o', label = 'setosa')
	    plt.scatter(x[50:100, 0], x[50:100, 1], color='blue', marker='*', label='versicolor')
	    #plt.xlabel('length of the huajing')
	    # plt.ylabel('length of the huaban')
	    # plt.legend(loc = 'upper right')
	    # plt.show()
	    p1 = Perceptron(eta = 0.1)
	    p1.fit(x, y)
	    # plt.plot(range(1, len(p1.errors_) + 1), p1.errors_, marker = 'o')
	    # plt.xlabel('Epochs')
	    # plt.ylabel('error sort')
	    # plt.show()
	    plot_decision_regions(x, y, p1)
	    pass
	if __name__ == '__main__':
	    main()
	```
结果
<center>
<img src="./a6.png" style="width:500px;">
</center>

## 适应性线性神经元算法

<center>
<img src="./b1.png" style="width:500px;">
</center>

和方差 & 渐进下降法
<center>
<img src="./b2.png" style="width:300px;"><span> </span><img src="./b3.png" style="width:400px;">
</center>

和方差求偏导数 & 神经元参数的更新
<center>
<img src="./b4.png" style="width:200px;"><span> </span><img src="./b5.png" style="width:200px;">
</center>

编码实现

	```py
	# -*- coding: UTF-8 -*-
	import numpy as np
	import pandas as pd
	import matplotlib.pyplot as plt
	# 适应性线性神经元算法
	class AdallineGD(object):
	    """
	    eta: float 学习效率，处于0和1
	    n_iter: int 迭代次数,对训练数据进行学习改进的次数
	    w_:一维向量 存储权重数值
	    errpr_: int 存储每次迭代改进时，进行错误判断的次数
	    """
	    def __init__(self,eta=0.01,n_iter=50):
	        self.eta=eta
	        self.n_iter=n_iter
	    def fix(self,X,y):
	        """
	        :param X: 二维数组，[n_samples,n_features],
	        n_samples：表示 X 中含有训练数据条目数
	        n_features：含有4 个数据的一维向量，用于表示一条训练条目  
	        :param y: 一维向量，用于存储每一条目对应的正确分类
	        :return: 
	        """
	        # 权重初始化为 0
	        self.w_=np.zeros(1+X.shape[1])
	        self.cost_=[]
	        for i in range(self.n_iter):
	            # out=w*1+w1*x1+...+wn*xn
	            output=self.net_input(X)
	            errors=(y-output)
	            # 改进权重分量
	            self.w_[1:]+=self.eta*X.T.dot(errors)
	            self.w_[0]+=self.eta*errors.sum()
	            cost=(errors**2).sum()/2.0
	            self.cost_.append(cost)
	        return self
	    # 做一个点积运算   W × X
	    def net_input(self, X):
	            # z=w0*1+w1*x1+...+wn*xn
	            return np.dot(X, self.w_[1:]) + self.w_[0]
	    #激活函数
	    def activation(self,X):
	        return self.net_input(X)
	    #结果函数
	    def predict(self, X):
	        return np.where(self.net_input(X) >= 0.0, 1, -1)
	from matplotlib.colors import ListedColormap
	def plot_decision_regions(x, y, classifier, resolution = 0.02):
	    marker = ('s', 'x', 'o', 'v')
	    colors = ('red', 'blue', 'green', 'gray', 'gray', 'cyan')
	    cmap = ListedColormap(colors[:len(np.unique(y))])
	    x1_min, x1_max = x[: , 0].min() - 1, x[: , 0].max()
	    x2_min, x2_max = x[: , 1].min() - 1, x[: , 1].max()
	    print x1_min, x1_max, x2_min, x2_max
	    # 185 从3.3- 6.98 每隔0.02
	    # 255 从0  - 5.08 每隔0.02
	    # xx1   从3.3 - 6.98 为一行  有185行相同的数据
	    # xx2   从0   - 5.08 为一列  第一行全为0 第二行全1 (255, 185)
	    xx1, xx2 = np.meshgrid(np.arange(x1_min, x1_max, resolution),
	                           np.arange(x2_min, x2_max, resolution))
	    # 相当于 np.arange(x1_min, x1_max, resolution) np.arange(x2_min, x2_max, resolution)
	    # 已经在分类了站如果是3.3 0 则为1 6.94 5.08 则-1
	    z = classifier.predict(np.array([xx1.ravel(), xx2.ravel()]).T)
	    print xx1.ravel()
	    print xx2.ravel()
	    print z
	    z = z.reshape(xx1.shape)
	    print z
	    # 在两个分类之间画分界线
	    plt.contourf(xx1, xx2, z, alpha = 0.4, cmap = cmap)
	    plt.xlim(x1_min, x1_max)
	    print "xx1.min()", x1_min
	    plt.ylim(xx2.min(), xx2.max())
	    plt.xlabel('length of the huaban')
	    plt.ylabel('length of the huajing')
	    plt.legend(loc='upper left')
	    plt.show()
	def main():
	    df = pd.read_csv('iris.csv', header=None)
	    y = df.loc[0:99, 4].values
	    print y.shape
	    y = np.where(y == 'Iris-setosa', 1, -1)
	    X = df.iloc[0:100, [0, 2]].values
	    plt.scatter(X[:50, 0], X[:50, 1], color='red', marker='o', label='setosa')
	    plt.scatter(X[50:100, 0], X[50:100, 1], color='blue', marker='*', label='versicolor')
	    #初始化
	    ada = AdallineGD(eta=0.0001, n_iter=50)
	    ada.fix(X,y)
	    plot_decision_regions(X,y,classifier=ada, resolution=0.02)
	    #错误判断的次数
	    plt.plot(range(1, 51), ada.cost_, marker='o')
	    plt.show()
	if __name__ == '__main__':
	    main()
	```

结果

<center>
<img src="./b6.png" style="width:500px;">
</center>

<center>
<img src="./b7.png" style="width:500px;">
</center>
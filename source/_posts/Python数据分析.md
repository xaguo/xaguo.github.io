---
title: Python数据分析
date: 2017-09-30 09:18:08
tags: [Python,数据分析]
---

使用Python进行数据分析的基本模块。介绍的模块主要为六个：numpy、scipy、matplotlib、pandas、scikit-learn、keras。
<!-- more -->

Python 特点：简洁，开发效率高，运算速度快，胶水特性。

数据分析：numpy、scipy、matplotlib、pandas,scikit-learn、keras…

numpy:数据结构基础

scipy:强大的科学计算方法(矩阵分析、信号分析、数理分析…)

matplotlib:丰富的可视化套件

pandas:基础数据分析套件

scikit-learn:强大的数据分析建模库（数据分析）

keras:人工神经网络

安装 [Anaconda](https://www.anaconda.com/download/#windows)

## numpy

开源 数据计算扩展

功能： ndarray 多维操作 线性代数

[numpy官网](http://www.numpy.org/)

numpy 编码Demo

	```py
	# -*- coding: UTF-8 -*-
	import numpy as np
	def main():
	    lst=[[1,3,5],[2,4,6]]
	    print (type(lst))
	    # 1 numpy.array
	    np_lst=np.array(lst)
	    print(type(np_lst))
	    # 设置 numpy.array 的数据类型
	    # bool,int,int8/16/32/64/128/,uint,uint8/16/32/64/128,float,float16/32/64/128
	    np_lst=np.array(lst,dtype=np.float)
	    # 设置 np.arra/y 的属性
	    print(np_lst.shape)#形状(行列)
	    print(np_lst.ndim)#维度
	    print(np_lst.dtype)#数据类型
	    print(np_lst.itemsize)#每个元素的大小
	    print(np_lst.size)#元素个数
	    #2 numpy 中常用的 array
	    print(np.zeros([2,4]))#0 数组
	    print(np.ones([3,5]))#1 数组
	    print("rand:")
	    print(np.random.rand(2,4))#两行四列随机数
	    print(np.random.rand())#一个机数
	    print("randInt:")
	    print(np.random.randint(1,10))#打印随机整数
	    print(np.random.randint(1,10,3))#打印 3 个随机整数
	    print("randn:")
	    print(np.random.randn())#打印正态分布随机
	    print(np.random.randn(2,4))#打印 2 行 4 列正态分布随机
	    print("Choice:")
	    print(np.random.choice([10,20,30,40]))#给定数的随机
	    print("Distribute:")
	    print(np.random.beta(1,10,100))#beta 分布
	    # 3 numpy 中常用操作
	    print(np.arange(1,11).reshape([2,5]))
	    print(np.arange(1,11).reshape([2,-1]))
	    lst=np.arange(1,11).reshape([2,-1])
	    print("exp:")
	    print(np.exp(lst))#自然指数
	    print("exp2:")
	    print(np.exp2(lst))#自然指数的平方
	    print("sqrt:")
	    print(np.sqrt(lst))#开平方
	    print("sin:")
	    print(np.sin(lst))#三角函数
	    print("log:")
	    print(np.log(lst))#对数
	    lst=np.array([
	      [
	          [1,2,3,4],
	          [4,5,6,7]
	      ],
	      [
	          [7,8,9,10],
	          [10,11,12,13]
	      ],
	      [
	          [14,15,16,17],
	          [18,19,20,21]
	      ]
	    ])
	    print("sum():")
	    print(lst.sum())
	    print("sum(axis=0):")
	    print(lst.sum(axis=0))
	    print("sum(axis=1):")
	    print(lst.sum(axis=1))
	    print("sum(axis=2):")
	    print(lst.sum(axis=2))
	    print("max(axis=1):")
	    print(lst.max(axis=1))
	    print("min(axis=0):")
	    print(lst.min(axis=0))
	    lst1=np.array([10,20,30,40])
	    lst2=np.array([4,3,2,1])
	    print("add:")
	    print(lst1+lst2)
	    print("sub:")
	    print(lst1-lst2)
	    print("mul:")
	    print(lst1*lst2)
	    print("div:")
	    print(lst1/lst2)
	    print("square:")
	    print(lst1**2)
	    print("dot:")
	    print(np.dot(lst1.reshape([2,2]),lst2.reshape([2,2])))
	    print("concatenate:")
	    print(np.concatenate((lst1,lst2),axis=0))
	    print(np.vstack((lst1,lst2)))
	    print(np.hstack((lst1,lst2)))
	    print(np.split(lst1,2))
	    print(np.split(lst1,4))
	    print(np.copy(lst1))
	    #4 numpy 矩阵操作与线性方程组
	    from numpy.linalg import *
	    print(np.eye(3))#单位矩阵
	    lst=np.array([[1,2],[3,4]])
	    print("inv:")
	    print(inv(lst))#矩阵逆
	    print("T:")
	    print(lst.transpose())#矩阵反置
	    print("det:")
	    print(det(lst))#矩阵行列式
	    print("eig:")
	    print(eig(lst))#矩阵特征值特征向量
	    lst = np.array([[1, 2], [3, 4]])
	    y=np.array([[5,],[7,]])
	    print("solve:")
	    print(solve(lst,y))#解线性方程组
	    #numpy 其它应用简介
	    print("FFT:")
	    print(np.fft.fft(np.array([1,1,1,1,1,1,1,1])))
	    print("Coef:")
	    print(np.corrcoef([1,0,1],[0,2,1]))
	    print("Poly:")
	    print(np.poly1d([2,1,3]))
	if __name__=="__main__":
	    main()
	```

## maplotlib

python 绘图库

[maplotlib](http://matplotlib.org/api/pyplot_api.html)

- 折线图

	```py
	# -*- coding: UTF-8 -*-
	import numpy as np
	import matplotlib.pyplot as plt
	def main():
	    #line
	    x = np.linspace(-np.pi,np.pi,256,endpoint=True)
	    c,s=np.cos(x),np.sin(x)#余弦,正弦
	    plt.figure(1)
	    plt.plot(x, c,color="blue",linewidth="1.0",linestyle="-",label="COS",alpha=0.2)
	    plt.plot(x, s,"r*",label="SIN")
	    plt.title("COS & SIN")#设置标题
	    ax=plt.gca()
	    ax.spines["right"].set_color("none")
	    ax.spines["top"].set_color("none")
	    ax.spines["left"].set_position(("data",0))
	    ax.spines["bottom"].set_position(("data",0))
	    #设置显示位置
	    ax.xaxis.set_ticks_position("bottom")
	    ax.yaxis.set_ticks_position("left")
	    #设置显示间隔
	    plt.xticks([-np.pi,-np.pi/2,0,np.pi/2,np.pi],
	    [r'$-\pi$',r'$-\pi/2$',r'$0$',r'$+\pi/2$',r'$+\pi$'])
	    plt.yticks(np.linspace(-1,1,5,endpoint=True))
	    #设置显示字体大小
	    for label in ax.get_xticklabels()+ax.get_yticklabels():
	        label.set_fontsize(16)
	        label.set_bbox(dict(facecolor="white",edgecolor="None",alpha=0.2))
	    plt.legend(loc="upper left")#设置图例
	    plt.grid()#设置网格线
	    #plt.axis([-1,1,-0.5,1])#指定显示范围
	    plt.fill_between(x,np.abs(x)<0.5,c,c>0.5,color="green",alpha=0.5)
	    #加注释
	    t=1
	    plt.plot([t,t],[0,np.cos(t)],"y",linewidth=3,linestyle="--")
	    plt.annotate("cos(1)", xy=(t, np.cos(1)), xycoords="data", xytext=(+10, +30),
	                 textcoords="offset points", arrowprops=dict(arrowstyle="->", connectionstyle="arc3,rad=.2"))
	    plt.savefig("./data/fig.png")  # 保存图片
	    plt.show()
	if __name__=="__main__":
	    main()
	```

输出
<center>
<img src="./fig.png" style="width=400px">
</center>

- 子图与其他图形的绘制

	```py
	# -*- coding: UTF-8 -*-
	import numpy as np
	import matplotlib.pyplot as plt
	def main():
	    #scatter
	    fig=plt.figure()
	    ax=fig.add_subplot(3,3,1)
	    n=128
	    X=np.random.normal(0,1,n)
	    Y=np.random.normal(0,1,n)
	    T=np.arctan2(Y,X)
	    # plt.axes([0.025,0.025,0.95,0.95])
	    ax.scatter(X,Y,s=0.75,c=T,alpha=1)
	    plt.xlim(-1.5,1.5),plt.xticks([])
	    plt.ylim(-1.5,1.5),plt.yticks([])
	    plt.axis()
	    plt.title("scatter")
	    plt.xlabel("x")
	    plt.ylabel("y")
	    #bar
	    fig.add_subplot(332)
	    n=5
	    X=np.arange(n)
	    Y1=(1-X/float(n)*np.random.uniform(0.5,1.0,n))
	    Y2=(1-X/float(n)*np.random.uniform(0.5,1.0,n))
	    plt.bar(X,+Y1,facecolor="#9999ff",edgecolor="white")
	    plt.bar(X,-Y1,facecolor="#ff9999",edgecolor="white")
	    for x,y in zip(X,Y1):
	        plt.text(x+0.4,y+0.05,'%.2f'%y,ha='center',va='bottom')
	    for x, y in zip(X, Y2):
	        plt.text(x+0.4,-y-0.05,'%.2f'%y,ha='center',va='top')
	    #pie
	    fig.add_subplot(333)
	    n=15
	    Z=np.ones(n)
	    Z[-1]*=2
	    plt.pie(Z,explode=Z*.05,colors=['%f'%(i/float(n))for i in range(n)],
	            labels=['%.2f'%(i/float(n)) for i in range(n)])
	    plt.gca().set_aspect('equal')
	    plt.xticks([]),plt.yticks([])
	    #polar
	    fig.add_subplot(334)
	    n=20
	    theta=np.arange(0.0,2*np.pi,2*np.pi/n)
	    radii=10*np.random.rand(n)
	    plt.plot(theta,radii)
	    fig.add_subplot(338,polar=True)
	    n = 20
	    theta = np.arange(0.0, 2 * np.pi, 2 * np.pi / n)
	    radii = 10 * np.random.rand(n)
	    # plt.plot(theta, radii)
	    plt.polar(theta,radii)
	    #heatmap
	    fig.add_subplot(336)
	    from matplotlib import cm
	    data=np.random.rand(3,3)
	    cmap=cm.Blues
	    map=plt.imshow(data,interpolation='nearest',cmap=cmap,aspect='auto',vmin=0,vmax=1)
	    #3D
	    from mpl_toolkits.mplot3d import Axes3D
	    ax=fig.add_subplot(337,projection="3d")
	    ax.scatter(1,1,3,s=100)
	    #hot map
	    fig.add_subplot(339)
	    def f(x,y):
	        return (1-x/2+x**5+y**3)*np.exp(-x**2-y**2)
	    n=256
	    x=np.linspace(-3,3,n)
	    y=np.linspace(-3,3,n)
	    X,Y=np.meshgrid(x,y)
	    plt.contourf(X,Y,f(X,Y),8,alpha=.75,cmap=plt.cm.hot)
	    plt.savefig("./data/fig1.png")#保存图片
	    plt.show()
	if __name__=="__main__":
	    main()
	```

输出

<center>
<img src="./fig1.png" style="width=400px">
</center>

## scipy

数值计算库

[scipy官网](https://www.scipy.org/)

使用编码 Demo

	```py
	# -*- coding: UTF-8 -*-
	import numpy as np
	from pylab import *
	def main():
	    #1 - scipy Integral 积分
	    #1,2,n 维积分
	    from scipy.integrate import quad,dblquad,nquad
	    print(quad(lambda x:np.exp(-x),0,np.inf))#打印积分的值和误差
	    print(dblquad(lambda t,x:np.exp(-x*t)/t**3,0,np.inf,lambda  x:1,lambda x:np.inf))
	    def f(x,y):
	        return x*y
	    def bound_y():
	        return [0,0.5]
	    def bound_x(y):
	        return [0,1-2*y]
	    print(nquad(f,[bound_x,bound_y]))
	    #2-scipy Optimizer 优化器
	    from scipy.optimize import minimize
	    #求函数的全局最小值
	    def rosen(x):
	        return sum(100.0*(x[1:]-x[:-1]**2.0)**2.0+(1-x[:-1])**2.0)
	    x0=np.array([1.3,0.7,0.8,1.9,1.2])
	    res=minimize(rosen,x0,method="nelder-mead",options={"xtol":1e-8,"disp":True})
	    print("Rose MINI:",res.x)
	    #求约束条件下的最小值
	    # def func(x):
	    #     return (2*x[0]*x[1]+2*x[0]-x[0]**2-2*x[1]**2)
	    # def func_deriv(x):
	    #     dfdx0=(-2*x[0]+2*x[1]+2)
	    #     dfdx1=(2*x[0]-4*x[1])
	    #     return np.array([dfdx0,dfdx1])
	    # cons=({"type":"eq","fun":lambda x:np.array([x[0]**3-x[1]]),"jac":lambda x:np.array([3.0*(x[0]**2.0)-1.0])},
	    #     {'type':'ineq','fun':lambda x:np.array([x[1]-1]),'jac':lambda x:np.array([0.0,1.0])}
	    #       )
	    # res1=minimize(func,np.array([-1.0,1.0]),jac=func_deriv,constraints=cons,method='SLSQP',options={'disp':True})
	    # print("RESTERICT",res1)
	    #求根
	    from scipy.optimize import root
	    def fun(x):
	        return x+2*np.cos(x)
	    sol=root(fun,0.1)
	    print("Root",sol.x,sol.fun)
	    #3-scipy Interpolation 插值
	    x=np.linspace(0,1,10)
	    y=np.sin(2*np.pi*x)
	    from scipy.interpolate import interp1d
	    li=interp1d(x,y,kind="cubic")
	    x_new=np.linspace(0,1,50)
	    y_new=li(x_new)
	    figure()
	    plot(x,y,"r")
	    plot(x_new,y_new,"k")
	    show()
	    print(y_new)
	    #4-scipy Linear 线性计算与矩阵分解
	    from scipy import linalg as lg
	    arr=np.array([[1,2],[3,4]])
	    print("Det:",lg.det(arr))#矩阵行列式
	    print("Inv:",lg.inv(arr))#矩阵的逆
	    #解线性方程组
	    b=np.array([6,14])
	    print("Sol:",lg.solve(arr,b))
	    print("Eig:",lg.eig(arr))#特征值、特征向量
	    #矩阵分解
	    print("LU:",lg.lu(arr))#LU分解
	    print("QR:",lg.qr(arr))#QR分解
	    print("SVD:",lg.svd(arr))#奇异值分解
	    print("Schur:",lg.schur(arr))#布尔分解
	    #5-scipy 学习方法：官网
	if __name__ == '__main__':
	    main()
	```

## pandas

数据分析库

[pandas官网](http://pandas.pydata.org/)

	```py
	# -*- coding: UTF-8 -*-
	import numpy as np
	import pandas as pd
	def main():
	    #数据结构 Data structure
	    s=pd.Series([i*2 for i in range(1,10)])
	    print(type(s))
	    print(s)
	    datas=pd.date_range("20171001",periods=8)
	    df=pd.DataFrame(np.random.rand(8,5),index=datas,columns=list("ABCDE"))
	    print(df)
	    # df=pd.DataFrame({"A":1,"B":pd.Timestamp("20171001"),"C":pd.Series(1,index=list(range(4)),dtype="float32"),
	    #                  "D":np.array([3]*4,dtype="float32"),"E":pd.Categorical(["police","student","teacher","doctor"])})
	    # print(df)
	    #Basic 基本操作
	    print(df.head(3))#前几行
	    print(df.tail(3))#后几行
	    print(df.index)#
	    print(df.values)#
	    print(df.T)#转置
	    #？ print(df.sort(columns="C"))#排序
	    print(df.sort_index(axis=1,ascending=False))#排序
	    print(df.describe())#
	    #Select 选择(切片)
	    print(type(df["A"]))
	    print(df["A"])
	    print(df[:3])
	    print(df["20171001":"20171004"])
	    print(df.loc[datas[0]])
	    print(df.loc["20171001":"20171004",["B","D"]])#指定行列
	    print(df.at[datas[0],"C"])#指定值
	    print(df.iloc[1:3,2:4])#通过下标选择
	    print(df.iloc[1,4])#通过下标选择
	    print(df.iat[1,4])#通过下标选择
	    print(df[df.B>0.5])#通过条件选择
	    print(df[df.B>0.5][df.A>0.5])#通过条件选择
	    print(df[df>0.5])#通过条件选择
	    print(df[df["E"].isin([1,0.682477])])#通过条件选择
	    #Set
	    s1=pd.Series(list(range(10,18)),index=pd.date_range("20171001",periods=8))
	    df["F"]=s1
	    print(df)
	    df.at[datas[0],"A"]=0
	    print(df)
	    df.iat[1,1]=1
	    df.loc[:,"D"]=np.array([4]*len(df))
	    print(df)
	    df2=df.copy()
	    df2[df2>1]=-df2
	    print(df2)
	    #pandas 缺失值处理
	    df1=df.reindex(index=datas[0:4],columns=list("ABCD")+["G"])
	    df1.loc[datas[0]:datas[1],"G"]=1
	    print(df1)
	    print(df1.dropna())#丢弃处理
	    print(df1.fillna(value=-1))#填充指定值
	    #pandas 表统计与拼接
	    print(df.mean())#均值
	    print(df.var())#方差
	    s=pd.Series([1,2,2,np.nan,5,7,9,10],index=datas)
	    print(s)
	    print(s.shift(2))
	    print(s.diff())
	    print(s.value_counts())#统计每个值出现的次数
	    print(df.apply(np.cumsum))#累加
	    print(df.apply(lambda x:x.max()-x.min()))#极差
	    #表格拼接
	    print(df[0:3])
	    pieces=[df[0:3],df[-3:]]#前三行和后三行
	    print(pd.concat(pieces))#拼接
	    left=pd.DataFrame({"key":["x","y"],"value":[1,2]})
	    right=pd.DataFrame({"key":["x","z"],"value":[3,4]})
	    print("LEFT",left)
	    print("RIGHT",right)
	    print(pd.merge(left,right,on="key",how="left"))
	    print(pd.merge(left,right,on="key",how="inner"))
	    print(pd.merge(left,right,on="key",how="outer"))
	    df3=pd.DataFrame({"A":["a","b","c","b"],"B":list(range(4))})
	    print(df3)
	    print(df3.groupby("A").sum())
	    #Reshape
	    import datetime
	    df4=pd.DataFrame({'A':['one','one','two','three']*6,
	                      'B':['a','b','c']*8,
	                      'C':['foo','foo','foo','bar','bar','bar']*4,
	                      'D':np.random.randn(24),
	                      'E':np.random.randn(24),
	                      'F':[datetime.datetime(2017,i,1)for i in range(1,13)]+
	                      [datetime.datetime(2017,i,15)for i in range(1,13)]
	                      })
	    print(df4)
	    print(pd.pivot_table(df4,values="D",index=["A","B"],columns=["C"]))
	    #pandas 时间、绘图、文件操作
	    #Time Series 时间
	    t_exam=pd.date_range("20171001",periods=10,freq="S")
	    print(t_exam)
	    #Graph 绘图
	    ts=pd.Series(np.random.randn(1000),index=pd.date_range("20171001",periods=1000))
	    ts.cumsum()
	    from pylab import *
	    ts.plot()
	    show()
	    #File 文件操作
	    df6=pd.read_csv("./data/test.csv")
	    print("CSV:",df6)
	    df7=pd.read_excel("./data/test.xlsx","Sheet1")
	    print("Excel:",df7)
	    df6.to_csv("./data/test2.csv")#保存
	    df7.to_excel("./data/test2.xlsx")#保存
	if __name__ == '__main__':
	    main()
	```

scikit-learn

数据挖掘建模，机器学习

[scikit-learn 官网](http://scikit-learn.org/)

使用编码Demo

	```py
	# -*- coding: UTF-8 -*-
	import numpy as np
	import pandas as pd
	#sklearn 实现决策树
	def main():
	    #数据预处理
	    from sklearn.datasets import load_iris
	    iris=load_iris()
	    print(iris)
	    print(len(iris["data"]))
	    from sklearn.cross_validation import train_test_split
	    train_data,test_data,train_target,test_target=train_test_split(iris.data,iris.target,test_size=0.2,random_state=1)
	    #数据建模
	    from sklearn import tree
	    clf=tree.DecisionTreeClassifier(criterion="entropy")
	    clf.fit(train_data,train_target)
	    y_pred=clf.predict(test_data)
	    # 结果验证
	    from sklearn import metrics
	    print(metrics.accuracy_score(y_true=test_target,y_pred=y_pred))
	    print(metrics.confusion_matrix(y_true=test_target,y_pred=y_pred))
	    #输出到文件
	    with open("./data/tree.dot","w") as fw:
	        tree.export_graphviz(clf,out_file=fw)
	if __name__ == '__main__':
	    main()
	```

## keras

人工神经网络

[keras 官网](https://keras.io/)

安装：

Anaconda CMD

`conda install mingw libpython`

`pip install keras`

使用编码Demo

	```py
	# -*- coding: UTF-8 -*-
	import numpy as np
	from keras.models import Sequential
	from keras.layers import Dense,Activation
	from keras.optimizers import SGD
	#keras 实现简单的神经网络
	def main():
	    from sklearn.datasets import load_iris
	    iris = load_iris()
	    print(iris["target"])
	    from sklearn.preprocessing import LabelBinarizer
	    #标签化
	    print(LabelBinarizer().fit_transform(iris["target"]))
	    from sklearn.cross_validation import train_test_split
	    train_data,test_data,train_target,test_target=train_test_split(iris.data,iris.target,test_size=0.2,random_state=1)
	    labels_train=LabelBinarizer().fit_transform(train_target)
	    labels_test=LabelBinarizer().fit_transform(test_target)
	    model=Sequential(
	        [
	            Dense(5,input_dim=4),
	            Activation("relu"),
	            Dense(3),
	            Activation("sigmoid"),
	        ]
	    )
	    # model=Sequential()
	    # model.add(Dense(5,input=4))
	    #优化器
	    sgd=SGD(lr=0.01,decay=1e-6,momentum=0.9)
	    model.compile(optimizer=sgd,loss="categorical_crossentropy")
	    model.fit(train_data,labels_train,nb_epoch=200,batch_size=40)
	    print (model.predict_classes(test_data))
	    model.save_weights("./data/w")
	    model.load_weights("./data/w")
	if __name__ == '__main__':
	    main()
	```




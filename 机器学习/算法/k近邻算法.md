# k近邻算法


## 1.1K- 近邻简介

### 1.1.1 k-近邻算法概念

K近邻（K-Nearest Neighbor, KNN）是一种基本的机器学习算法，所谓k进邻，就是k个最近的邻居的意思，说的是每个样本都可以用它最接近的K个邻居来代表。比如：判断一个人的人品，只需要观察与他来往最密切的几个人的人品的好坏可以得出，即“**近朱者赤，近墨者黑**“

### 1.1.2 简单理解k-进邻算法

![knn1](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/knn1.png)

### 1.1.3 knn的算法思想

> 计算已知类别中数据集的点与当前点的距离[即计算所有样本点与待分类样本之间的距离]
>
> 按照距离递增次序排序[计算完样本距离进行排序]
>
> 选取与当前点距离最小的k个点[选取距离样本最近的k个点]
>
> 确定前k个点所在类别的出现频率[针对这k个点，统计下各个类别分别有多少个]
>
> 返回前k个点出现频率最高的类别作为当前点的预测分类[k个点中某个类别最多，就将样本划归在那个类别

###          1.1.4 knn 算法流程

搜集数据：如网络爬取，数据库，文件

准备数据：格式化处理，对不同类别的数据进行统一的格式化处理

分析数据：观察数据特点，有没有缺失值

训练数据：不适用于knn算法

测试算法：计算错误率

应用算法：针对完善的模型进行分装重构，然后进行实际应用



## 1.2K近邻算法api初步使用

### 1.2.1主要任务

**1.了解sklearn工具的优点和包含内容**

**2.应⽤skyline中的api实现KNN算法的简单使⽤**

**3.机器学习流程复习**

​                            1.获取数据集

​                            2.数据基本处理

​                            3.特征工程

​                            4.机器学习

​                            5.模型评估![1638088020423](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1638088020423-1638344472292.png)

### **1** .2.2     Scikit-learn⼯具介绍

![1638154872566](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1638154872566-1638344472292.png)

1.Python语⾔的机器学习⼯具

2.Scikit-learn包括许多知名的机器学习算法的实现

3.Scikit-learn⽂档完善，容易上⼿，丰富的API

4.⽬前稳定版本0.19.1

（1）安装

```
 pip3 install scikit-learn==0.19.1
```

（2）Scikit-learn所包含的内容

​    在 Sklearn 里面有**六大任务模块：**分别是**分类、回归、聚类、降维、模型选择和预处理** 

![1638268760309](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1638268760309-1638344472292.png)

### 1.1.3    K近邻算法API

```python
 sklearn.neighbors.KNeighborsClassifier(n_neighbors=5) 关于k的取值我们后面会讲到
```



### 1.1.4   案例-初步了解KNeighborsClassifier

1.导入模块

```
from sklearn.neighbors import KNeighborsClassifier
```

2.数据处理

```
X = np.array([[-1, -1], [-2, -1], [-3, -2], [1, 1], [2, 1], [3, 2]])#调用numpy定义一个矩阵
y = [1, 1, 1, 0, 0, 0]                          # 分类值确定
```

3.调用fit方法进行训练

```
使⽤fit⽅法进⾏训练模型

estimator = KNeighborsClassifier(n_neighbors=2) # 使⽤fit⽅法进⾏训练确定k=2
estimator.fit(X, y)                             # 导入训练值
```

4.进行预测并输出结果

```
result = estimator.predict([[1, 0]])
print(result)
```

5.为了更好的展示算法特性，我们引入了**numpy**和**matplotlib**对代码内容进行展示。

```python
import numpy as np  # 导入 numpy
from sklearn.neighbors import KNeighborsClassifier  # 导入sklearn模块
import matplotlib.pyplot as plt  # 导入 matplotlib.plot

# 数据处理
X = np.array([[-1, -1], [-2, -1], [-3, -2], [1, 1], [2, 1], [3, 2]])  # 调用numpy模块定义一个矩阵
y = ["左下", "左下", "左下", "右上", "右上", "右上"]  # 类别

# 使⽤fit⽅法进⾏训练模型
estimator = KNeighborsClassifier(n_neighbors=2)  # 使⽤fit⽅法进⾏训练确定k=2
estimator.fit(X, y)  # 导入训练值 

# 进行预测并输出结果
result = estimator.predict([[1, 0]])
print("[1,0]点被归类为:", result)

# 可视化
plt.scatter(1, 0)  # 同第九行数值 使用matplotlib绘制预测点在图中的位置
plt.scatter(X[:, 0], X[:, 1])  # 对矩阵内元素进行切片
plt.show()

```

输出结果分析：[1,0]点被归类为: ['右上']

​                      ![1638269819888](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1638269819888-1638344472292.png)

​     通过上方输出结果我们可以看出，经过我么的6个数据的训练，我们的算法已经可以确认出我们指定的被预测点的相对方位为左上方还是右下方。**由此体现出我们的K近邻算法，是根据近邻数据从而确定自己的位置，从而实现一个分类的功能。**




## 1.3距离度量

### 学习目标

1. 了解距离公式的基本性质
2. 知道机器学习中常见的距离计算公式


### 1.3.1距离公式的基本性质

### 1.3.2常见的距离公式

1. 在机器学习过程中，对于函数 dist(., .)，若它是⼀"距离度量" (distance measure)，则需满⾜⼀些基本性质: 

2. **⾮负性**： dist(X , X ) >= 0 ；

3. **同⼀性**：dist(x , x ) = 0。当且仅当 X = X ；

4. **对称性**： dist(x , x ) = dist(x , x )；

5. **直递性**： dist(x , x ) <= dist(x , x ) + dist(x , x ) 直递性常被直接称为“三⻆不等式”

#### 1.3.2.1欧式距离

![image-20220904145915610](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/image-20220904145915610.png)



- ```python
  X=[[1,1],[2,2],[3,3],[4,4]]; 
   
  经计算得: 
  d = 1.4142 2.8284 4.2426 1.4142 2.8284 1.4142
  ```




#### 1.3.2.2曼哈顿距离(Manhattan Distance)

​		顾名思义，在曼哈顿街区要从一个十字路口开车到另一个十字路口，驾驶距离显然不是两点间的直线距离。这个实际驾驶距离就是“曼哈顿距离”。曼哈顿距离也称为“城市街区距离”(City Block distance)。

![image-20220904144444079](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/image-20220904144444079.png)

```python
 X=[[1,1],[2,2],[3,3],[4,4]]
    经计算得: d = 2 4 6 2 4 2
```

![image-20220904145523509](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/image-20220904145523509.png)

#### 1.3.2.3切比雪夫距离 (Chebyshev Distance)

国际象棋中，国王可以直行、横行、斜行，所以国王走一步可以移动到相邻8个方格中的任意一个。国王从格子(x1,y1)走到格子(x2,y2)最少需要多少步？这个距离就叫切比雪夫距离。

![image-20220904145504325](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/image-20220904145504325.png)

```python
X=[[1,1],[2,2],[3,3],[4,4]];

 经计算得: 

d = 1 2 3 1 2 1
```

#### 1.3.2.4闵可夫斯基距离(Minkowski Distance)：

 闵⽒距离不是⼀种距离，⽽是⼀组距离的定义，是对多个距离度量公式的概括性的表述。 两个n维变量a(x11,x12,…,x1n)与b(x21,x22,…,x2n)间的闵可夫斯基距离定义为

![image-20220904145549125](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/image-20220904145549125.png)

其中p是⼀个变参数：

- 当p=1时，就是曼哈顿距离；

- 当p=2时，就是欧⽒距离； 当

- p→∞时，就是切⽐雪夫距离。

根据p的不同，闵⽒距离可以表示某⼀类/种的距离

![1641639418093](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641639418093.png)

![1641639476145](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641639476145.png)

## 1.4 k值的选择

### 学习目标

- ⽬标 

知道KNN中K值⼤⼩选择对模型的影响 

知道估计误差和近似误差



![k](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/k.png)

### 1.4.1  k值的选择

1. k值过大，就相当于用较大领域中的训练实例进行预测，其优点是可以减少学习的估计误差，但缺点是学习的近似误差会增大。这时候，与输入实例较远（不相似的）训练实例也会对预测器作用，使预测发生错误，且K值的增大就意味着整体的模型变得简单   容易受到异常点的影响
2. k值过小  相当于用较小的领域中的训练实例进行预测，“学习”近似误差会减⼩，只有与输⼊实例较近或相似的训练实例才会对预测结果起作⽤，与此同时带来的问题 是“学习”的估计误差会增⼤， 换句话说，K值的减⼩就意味着整体模型变得复杂，容易发⽣过拟合；  受到样本均衡的问题
3. K=N（N为训练样本个数）,则完全不⾜取， 因为此时⽆论输⼊实例是什么，都只是简单的预测它属于在训练实例中最多的类，模型过于简单，忽略了训练 实例中⼤量有⽤信息。
4. 在实际应⽤中，K值⼀般取⼀个⽐较⼩的数值，例如采⽤交叉验证法（简单来说，就是把训练数据在分成两组:训练 k值的选择 18 集和验证集）来选择最优的K值。


### 1.4.2误差

近似误差： 对现有训练集的训练误差，关注训练集， 如果近似误差过⼩可能会出现过拟合的现象，对现有的训练集能有很好的预测，但是对未知的测试样本将会出 现较⼤偏差的预测。 模型本身不是最接近最佳模型。估计误差： 可以理解为对测试集的测试误差，关注测试集， 估计误差⼩说明对未知数据的预测能⼒好， 模型本身最接近最佳模型。



## 1.5 KD树

### 	1.5.1 问题导入：

实现K近邻算法时，主要考虑的问题就是**如何对训练数据进行快速的k近邻搜索**

​		k近邻法最简单的实现是**线性扫描（穷举搜索）**，即要计算输入实例与每一个训练实例的距离。计算并存储好以后，再查找K近邻。当训练集很大时，计算非常耗时。为了提高kNN搜索的效率，可以考虑使用特殊的结构存储训练数据，以减小计算距离的次数。这里介绍的就是KD树。

### 	1.5.2什么是kd树

​	根据**KNN**每次需要预测⼀个点时，我们都需要计算训练数据集⾥每个点到这个点的距离，然后选出距离最近的k个点进 ⾏投票。当数据集很⼤时，这个计算成本⾮常⾼，针对N个样本，D个特征的数据集，其算法复杂度O（***DN* **）。 

kd**树**：为了避免每次都重新计算⼀遍距离，算法会把距离信息保存在⼀棵树⾥，这样在计算之前从树⾥查询距离信息， 

尽量避免重新计算。其基本原理是，==如果A和B距离很远，B和C距离很近，那么A和C的距离也很远==。有了这个信息， 

就可以在合适的时候跳过距离远的点。 这样优化后的算法复杂度可降低到O（DNlog（N））。感兴趣的读者可参阅论⽂：Bentley，J.L.，Communications of the ACM（1975）。 

​	1989年，另外⼀种称为**Ball Tree**的算法(用来解决多维特征的算法)，在kd Tree的基础上对性能进⼀步进⾏了优化。感兴趣的读者可以搜索**Five** 

**balltree construction algorithms**来了解详细的算法信息。

### 1.5.3原理

---



![1641640141033](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641640141033.png)

​		⻩⾊的点作为根节点，上⾯的点归左⼦树，下⾯的点归右⼦树，接下来再不断地划分，**分割的那条线叫做分割超平⾯（splitting hyperplane）**，**在⼀维中是⼀个点，⼆维中是线，三维的是⾯。**

![1641640175230](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641640175230.png)

​	

⻩⾊节点就是Root节点，下⼀层是红⾊，再下⼀层是绿⾊，再下⼀层是蓝⾊。

![1641640190973](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641640190973.png)

**1.树的建立；**

**2.最近邻域搜索（Nearest-Neighbor Lookup）**

kd树(K-dimension  tree)**是⼀种对k维空间中的实例点进⾏存储以便对其进⾏快速检索的树形数据结构**。kd树是⼀种⼆叉树，表示对k维空间的⼀个划分**，构造kd树相当于不断地⽤垂直于坐标轴的超平⾯将K维空间切分，构成⼀系列的K维超矩形区域**。kd树的每个结点对应于⼀个k维超矩形区域。**利⽤kd树可以省去对⼤部分数据点的搜索，从⽽减少搜索的计算量。**

![1641640327033](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641640327033.png)

类⽐“⼆分查找”：给出⼀组数据：[9 1 4 7 2 5 0 3 8]，要查找8。如果挨个查找（线性扫描），那么将会把数据集都遍历⼀遍。⽽如果排⼀下序那数据集就变成了：[0 1 2 3 4 5 6 7 8 9]，按前⼀种⽅式我们进⾏了很多没有必要的查找，现在如果我们以5为分界点，那么数据集就被划分为了左右两个“簇” [0 1 2 3 4]和[6 7 8 9]。

因此，根本就没有必要进⼊第⼀个簇，**可以直接进⼊第⼆个簇进⾏查找**。把⼆分查找中的数据点换成k维数据点，这样的划分就变成了⽤超平⾯对k维空间的划分。空间划分就是对数据点进⾏分类，“挨得近”的数据点就在⼀个空间⾥⾯。



### 1.5.4 kd树的构造

​		**1.构造根节点，使根结点对应于K维空间中包含所有实例点的超矩形区域；**

​		**2.通过递归的方法，不断地对k维空间进行切分，生成子节点**。

​		**3.重复第二步骤，直到子区域中没有示例时终止**

​        **4.** 通常，循环的选择坐标轴对空间切分，选择训练实例点在坐标轴上的**中位数为切分点**，这样得到的kd树是平衡的（==平衡⼆叉树：它是⼀棵空树，或其左⼦树和右⼦树的深度之差的绝对值不超过1，且它的左⼦树和右⼦树都是平衡⼆叉树==）。

​		**5.需要关注细节：==a.选择向量的哪一维进行划分；==**

​                                      **==b.如何划分数据==**

​	:red_circle:**案例:**

​	 题目:给定一个二维空间数据集：T={(2,3),(5,4),(9,6),(4,7),(8,1),(7,2)}，构造一个平衡kd树。

![1641641262809](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641641262809.png)

​		根结点对应包含数据集T的矩形，选择x(1)轴，6个数据点的x(1)坐标中位数是6，这里选最接近的(7,2)点，以平面x(1)=7将空间分为左、右两个子矩形(子结点);

​		接着左矩形以x(2)=4分为两个子矩形（左矩形中{(2,3),(5,4),(4,7)}点的x(2)坐标中位数正好为4），右矩形以x(2)=6分为两个子矩形，如此递归，最后得到如下图所示的特征空间划分和kd树。

![1641641283592](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641641283592.png)

![1641643037371](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641643037371.png)

### 1.5.5最近邻域搜索

​        假设标记为星星的点是  test  point，  绿⾊的点是找到的近似点，在回溯过程中，需要⽤到⼀个队列，存储需要回溯的点，在判断其他⼦节点空间中是否有可能有距离查询点更近的数据点时，做法是==以查询点为圆⼼==，以当前的最近距离为 半径画圆，这个==圆称为候选超球==（candidate hypersphere），如果圆与回溯点的轴相交，则需要将轴另⼀边的节点都放到回溯队列⾥⾯来。

![1641641398681](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641641398681.png)

样本集{(2,3),(5,4), (9,6), (4,7), (8,1), (7,2)}

==查找点(2.1,3.1)==

![1641641465939](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641641465939.png)

在(7,2)点测试到达(5,4)，在(5,4)点测试到达(2,3)，然后search_path中的结点为<(7,2),(5,4), (2,3)>，从search_path中取出(2,3)作为当前最佳结点nearest, dist为0.141；==维护一个队列==

然后==回溯⾄(5,4)，以(2.1,3.1)为圆⼼，以dist=0.141为半径画⼀个圆，并不和超平⾯y=4相交==，如上图，所以不必跳到结点(5,4)的右⼦空间去搜索，因为右⼦空间中不可能有更近样本点了。

于是再==回溯⾄(7,2)，同理，以(2.1,3.1)为圆⼼，以dist=0.141为半径画⼀个圆并不和超平⾯x=7相交==，所以也不⽤跳到结点(7,2)的右⼦空间去搜索。

⾄此，search_path为空，==结束整个搜索，返回nearest(2,3)作为(2.1,3.1)的最近邻点，最近距离为0.141==。

 ==查找点(2,4.5)==

![1641641522112](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641641522112.png)

在(7,2)处测试到达(5,4)，在(5,4)处测试到达(4,7)【优先选择在本域搜索】，然后search_path中的结点为==<(7,2),(5,4), (4,7)>==，从search_path中取出(4,7)作为当前最佳结点nearest, ==dist为3.202==；

然后==回溯⾄(5,4)，以(2,4.5)为圆⼼，以dist=3.202为半径画⼀个圆与超平⾯y=4相交==，所以需要跳到(5,4)的左⼦空间去  搜索。所以要==将(2,3)加⼊到search_path中==，现在search_path中的结点为<(7,2),(2, 3)>；另外，(5,4)与(2,4.5)的距离为3.04 < dist = 3.202，所以将(5,4)赋给nearest，==并且dist=3.04==。

回溯⾄(2,3)，(2,3)是叶⼦节点，直接平判断(2,3)是否离(2,4.5)更近，==计算得到距离为1.5==，所以nearest更新为(2,3)，dist更新为(1.5)

回溯⾄(7,2)，同理，以(2,4.5)为圆⼼，以dist=1.5为半径画⼀个圆并不和超平⾯x=7相交, 所以不⽤跳到结点(7,2)的右⼦空间去搜索。

==⾄此，==search_path为空，结束整个搜索，==返回nearest(2,3)作为(2,4.5)的最近邻点，最近距离为1.5==。

**总结：**

**kd树的构建过程【知道】**

1.构造根节点
2.通过递归的⽅法，不断地对k维空间进⾏切分，⽣成⼦节点
3.重复第⼆步骤，直到⼦区域中没有示例时终⽌
需要关注细节：a.选择向量的哪⼀维进⾏划分；b.如何划分数据

**kd树的搜索过程【知道】**

1.⼆叉树搜索⽐较待查询节点和分裂节点的分裂维的值，（⼩于等于就进⼊左⼦树分⽀，⼤于就进⼊右⼦树分⽀直到叶⼦结点）

2.顺着“搜 索路径”找到最近邻的近似点

3.回溯搜索路径，并判断搜索路径上的结点的其他⼦结点空间中是否可能有距离查询点更近的数据点，如果有可     能，则需要跳到其他⼦结点空间中去搜索

4.重复这个过程直到搜索路径为空

## 1.6 案例一：鸢尾花种类预测--数据集介绍

​	Iris数据集是常⽤的分类实验数据集，由Fisher, 1936收集整理。Iris也称鸢尾花卉数据集，是⼀类多重变量分析的数据集。（共计150行）

![1638344956448](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1638344956448.png)

### 1.6.1    关于数据集的具体介绍：

<img src="https://pic-1313413291.cos.ap-nanjing.myqcloud.com/image-20220904150236481.png" alt="image-20220904150236481"  />

**收集数据**

Iris数据集是常用的分类实验数据集，由Fisher, 1936收集整理。Iris也称鸢尾花卉数据集，是一类多重变量分析的数据集

### 1.6.2 scikit-learn数据集API介绍

- sklearn.datasets
  - 加载获取流⾏数据
  - datasets.load_*()
    - 获取⼩规模数据集，数据包含在datasets⾥
  - datasets.fetch_*(data_home=None)
    - 获取⼤规模数据集，需要从⽹络上下载，函数的第⼀个参数是data_home，表示数据集下载的⽬录,默认是 ~/scikit_learn_data/

**sklearn小数据集**

​        sklearn.datasets.load_iris()

​        加载并返回鸢尾花数据集

<img src="https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641732477624.png" alt="1641732477624" style="zoom:150%;" />

**sklearn大数据集**

sklearn.datasets.fetch_20newsgroups(data_home=None,subset=‘train’)

- subset：'train'或者'test'，'all'，可选，选择要加载的数据集。	
- 训练集的“训练”，测试集的“测试”，两者的“全部”

### 1.6.3   sklearn数据集返回值的介绍

load和fetch返回的数据类型datasets.base.Bunch(字典格式)

- data：特征数据数组，是 [n_samples * n_features] 的⼆维 numpy.ndarray 数组
- target：标签数组，是 n_samples 的⼀维 numpy.ndarray 数组
- DESCR：数据描述
- feature_names：特征名,新闻数据，⼿写数字、回归数据集没有target_names：标签名
- target_names：标签名

```python
import sklearn.datasets
# 获取鸢尾花数据集
iris = sklearn.datasets.load_iris()
print("鸢尾花数据集的返回值：\n", iris)
# 返回值是一个继承自字典的Bench
print("鸢尾花的特征值:\n", iris["data"])
print("鸢尾花的目标值：\n", iris.target)
print("鸢尾花特征的名字：\n", iris.feature_names)
print("鸢尾花目标值的名字：\n", iris.target_names)
print("鸢尾花的描述：\n", iris.DESCR)
```

![image-20220904150357466](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/image-20220904150357466.png)

### 1.6.4   查看数据分布

**seaborn介绍**

:one:Seaborn 是基于 Matplotlib 核⼼库进⾏了更⾼级的 API 封装，可以让你轻松地画出更漂亮的图形。⽽ Seaborn的漂亮主要体现在配⾊更加舒服、以及图形元素的样式更加细腻。

:two:安装 pip3 install seaborn

:three:seaborn.lmplot() 是⼀个⾮常有⽤的⽅法，它会在绘制⼆维散点图时，⾃动完成回归拟合

- sns.lmplot() ⾥的 x, y 分别代表横纵坐标的列名, 
- data= 是关联到数据集,
- hue=代表按照 species即花的类别分类显示,
- fit_reg=是否进⾏线性拟合。

```python
from sklearn.datasets import load_iris
import seaborn as sns
import matplotlib.pyplot as plt
import pandas as pd

# 把数据转换成dataframe的格式
iris = load_iris()
iris_d = pd.DataFrame(iris['data'], columns=['Sepal_Length', 'Sepal_Width', 'Petal_Length', 'Petal_Width'])
iris_d['Species'] = iris.target


def plot_iris(iris, col1, col2):
    sns.lmplot(x=col1, y=col2, data=iris, hue="Species", fit_reg=False)
    plt.xlabel(col1)
    plt.ylabel(col2)
    plt.title('鸢尾花种类分布图')
    plt.show()


plot_iris(iris_d, 'Petal_Width', 'Sepal_Length')
```

![1641736243478](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641736243478.png)

### 1.6.5数据集的划分

机器学习一般的数据集会划分为两个部分：

**训练数据**：用于训练，构建模型
**测试数据**：在模型检验时使用，用于评估模型是否有效
划分比例：

训练集：70% 80% 75%
测试集：30% 20% 25%

**数据集划分api**

sklearn.model_selection.train_test_split(arrays, *options)
参数：
      x 数据集的特征值
      y 数据集的标签值
      test_size 测试集的大小，一般为float
      random_state 随机数种子,不同的种子会造成不同的随机采样结果。相同的种子采样结果相同。
return
      x_train, x_test, y_train, y_test
      先训练 后测试

![](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/%E9%B8%A2%E5%B0%BE%E8%8A%B1%E4%BB%A3%E7%A0%81.png)

```python
import seaborn as sns
import matplotlib.pyplot as plt
import pandas as pd
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
# 获取鸢尾花数据集
iris = load_iris()
# 对鸢尾花数据集进行分割
# 训练集的特征值x_train 测试集的特征值x_test 训练集的目标值y_train 测试集的目标值y_test
x_train, x_test, y_train, y_test = train_test_split(iris.data, iris.target, random_state=22)
print("x_train:\n", x_train.shape)
# 随机数种子
x_train1, x_test1, y_train1, y_test1 = train_test_split(iris.data, iris.target, random_state=6)
x_train2, x_test2, y_train2, y_test2 = train_test_split(iris.data, iris.target, random_state=6)
print("如果随机数种子不一致：\n", x_train == x_train1)
print("如果随机数种子一致：\n", x_train1 == x_train2)
```

```python
import seaborn as sns
import matplotlib.pyplot as plt
import pandas as pd
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
# 获取鸢尾花数据集
iris = load_iris()
print(iris)

iris_d = pd.DataFrame(iris['data'], columns = ['Sepal_Length', 'Sepal_Width', 'Petal_Length', 'Petal_Width'])
iris_d['Species'] = iris.target

print(iris_d)
# 对鸢尾花数据集进行分割
# 训练集的特征值x_train 测试集的特征值x_test 训练集的目标值y_train 测试集的目标值y_test
x_train, x_test, y_train, y_test = train_test_split(iris.data, iris.target,test_size=0.2, random_state=22)
print("x_train:\n", x_train.shape)
# 随机数种子
x_train1, x_test1, y_train1, y_test1 = train_test_split(iris.data, iris.target, random_state=6)
x_train2, x_test2, y_train2, y_test2 = train_test_split(iris.data, iris.target, random_state=3)
# print("训练集的特征值：\n", x_train1)
# print("测试集的特征值：\n", x_test1)
#
# print("训练集的目标值：\n", y_train1)
# print("测试集的目标值：\n", y_test1)
#
# print("训练集的目标值：\n", y_train)
# print("测试集的目标值：\n", y_test)

print("测试集的目标值：\n", y_test)

print("训练集的目标值：\n", y_test1)
print("测试集的目标值：\n", y_test2)


# print("如果随机数种子不一致：\n", x_train1)
# print("如果随机数种子一致：\n", x_train2)

# print("如果随机数种子不一致：\n", x_train == x_train1)
# print("如果随机数种子一致：\n", x_train1 == x_train2)
```



## 1.7特征工程-特征预处理

### 1.7.1学习目标

了解什么是特征预处理

知道归一化和标准化的原理及区别



### 1.7.2什么是特征预处理

​      特征预处理定义 scikit-learn的解释 provides several common utility functions and transformer classes to change raw feature vectors into a representation that is more suitable for the downstream estimators.

 **翻译过来：通过⼀些转换函数将特征数据转换成更加适合算法模型的特征数据过程**

![image-20220904150528830](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/image-20220904150528830.png)

**为什么我们要进⾏归⼀化/标准化？** 

特征的单位或者⼤⼩相差较⼤，或者==某特征的⽅差相⽐其他的特征要⼤出⼏个数量级，容易影响（⽀配）⽬标结果==，使得⼀些算法⽆法学习到其它的特征

![1641774096195](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641774096195.png)

我们需要⽤到⼀些⽅法进⾏**⽆量纲化**，**使不同规格的数据转换到同⼀规格**

包含内容（数值型数据的无量纲化）

- 归一化
- 标准化

特征预处理的API

```
sklearn.preprocessing
```



### 1.7.3归一化

---

**定义:** ==通过对原始数据进⾏变换把数据映射到(默认为[0,1])之间== 

**公式**

![guiyihua6](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/guiyihua6.png)

作⽤于每⼀列，max为⼀列的最⼤值，min为⼀列的最⼩值,那么X’’为最终结果，mx，mi分别为指定区间值默认 mx为1,mi为0

那么怎么理解这个过程呢？我们通过⼀个例⼦

<img src="https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641774256368.png" alt="1641774256368" style="zoom:150%;" />

---

**API介绍**

sklearn.preprocessing.MinMaxScaler (feature_range=(0,1)… ) 

​     MinMaxScalar.fit_transform(X)

​               X:numpy array格式的数据[n_samples,n_features]

​     返回值：转换后的形状相同的array

==只对特征值进行特征预处理==

**步骤：**

1.实例化MinMaxScalar
2.通过fit_transform转换

```python
import pandas as pd
from sklearn.preprocessing import MinMaxScaler

def minmax_demo(): 
   """
   归⼀化演示
   :return: None
   """
   data = pd.read_csv("./data/dating.txt") 
   print(data)
   # 1、实例化⼀个转换器类
   transfer = MinMaxScaler(feature_range=(2, 3)) //给一个范围
   # 2、调⽤fit_transform  将需要的数据放入
   data = transfer.fit_transform(data[['milage','Liters','Consumtime']]) //二维且只有特征值
   print("最⼩值最⼤值归⼀化处理的结果：\n", data)

return None
```

**结果**

![1641775079655](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641775079655.png)

![1641775233787](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641775233787.png)

**归⼀化总结**

注意最⼤值最⼩值是变化的，另外，最⼤值与最⼩值⾮常容易受异常点影响，所以这种⽅法鲁棒性较差，==只适合传统精确⼩数据场景==。

---

### 1.7.4标准化

**定义：**

通过对原始数据进⾏变换把数据变换到==均值为0,标准差为1==范围内

![Snipaste_2021-11-28_19-27-53](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/Snipaste_2021-11-28_19-27-53.png)

**作⽤于每⼀列**，==mean为平均值，σ为标准差==

**对于归⼀化来说：**==如果出现异常点，影响了最⼤值和最⼩值，那么结果显然会发⽣改变== 

**对于标准化来说：**如果出现异常点，==由于具有⼀定数据量，少量的异常点对于平均值的影响并不⼤，从⽽⽅差改变较⼩==

---

**API介绍**

sklearn.preprocessing.StandardScaler( )

​      处理之后每列来说所有数据都聚集在均值0附近标准差差为1 

​     StandardScaler.fit_transform(X)

​            X:numpy array格式的数据[n_samples,n_features]

​     返回值：转换后的形状相同的array

**数据计算**

同样对上面数据进行处理

1.实例化StandardScaler 

2.通过fit_transform转换

```python
import pandas as pd
from sklearn.preprocessing import StandardScaler

def stand_demo():
   """
   标准化演示
   :return: None 
   """
   data = pd.read_csv("dating.txt") print(data)
   # 1、实例化⼀个转换器类
   transfer = StandardScaler() 
   # 2、调⽤fit_transform
   data = transfer.fit_transform(data[['milage','Liters','Consumtime']]) 
   print("标准化的结果:\n", data)
   print("每⼀列特征的平均值：\n", transfer.mean_) 
   print("每⼀列特征的⽅差：\n", transfer.var_)

return None
```

**结果**

![1641778175628](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641778175628.png)

**标准化总结** 

**在已有样本⾜够多的情况下⽐较稳定，适合现代嘈杂⼤数据场景。**

---

### 1.7.5knn算法总结

**优点：**

- **简单有效**
- **重新训练的代价低**      
- **适合类域交叉样本**
  -  **KNN⽅法主要靠周围有限的邻近的样本**,⽽不是靠判别类域的⽅法来确定所属类别的，因此对于类域的交 叉或重叠较多的待分样本集来说，KNN⽅法较其他⽅法更为适合。
- **适合⼤样本⾃动分类**
  - 该算法⽐较适⽤于样本容量⽐**较⼤的类域的⾃动分类**，⽽那些==样本容量较⼩的类域==采⽤这种算法⽐较容易 产⽣误分。

---

**缺点：  **

- **惰性学习**
  - KNN算法是**懒散学习⽅法**（lazy learning,基本上不学习），⼀些积极学习的算法要快很多 
- **类别评分不是规格化** 
  -  不像⼀些通过概率评分的分类 
- **输出可解释性不强** 
  - 例如决策树的输出可解释性就较强
- **对不均衡的样本不擅⻓** 
  - 当样本不平衡时，如⼀个类的样本容量很⼤，==⽽其他类样本容量很⼩时，有可能导致当输⼊⼀个新样本时==，该样本的K个邻居中⼤容量类的样本占多数。该算法只计算“最近的”邻居样本，某⼀类的样本数量很 ⼤，那么或者这类样本并不接近⽬标样本，或者这类样本很靠近⽬标样本。⽆论怎样，数量并不能影响运 ⾏结果。==可以采⽤权值的⽅法（和该样本距离⼩的邻居权值⼤）来改进==。 
- **计算量较⼤** 
  - ⽬前常⽤的解决⽅法是事先对==已知样本点进⾏剪辑，事先去除对分类作⽤不⼤的样本==。

---

 

## 1.8 案例二：鸢尾花种类预测—流程实现

**近邻算法API再次介绍**

```python
sklearn.neighbors.KNeighborsClassifier(n_neighbors=5,algorithm='auto')
```

n_neighbors：
		int,可选（默认= 5），k_neighbors查询默认使用的邻居数
algorithm：{‘auto’，‘ball_tree’，‘kd_tree’，‘brute’}
		快速k近邻搜索算法，==默认参数为auto，可以理解为算法自己决定合适的搜索算法==。除此之外，用户也可以自		己指定搜索算法ball_tree、kd_tree、brute方法进行搜索，
				:small_red_triangle_down:brute是蛮力搜索，也就是线性扫描，当训练集很大时，计算非常耗时。
				:small_red_triangle_down:kd_tree，构造kd树存储数据以便对其进行快速检索的树形数据结构，kd树也就是数据结构中的二叉				     树。以中值切分构造的树，每个结点是一个==超矩形==，==在维数小于20时效率高==。

​                :small_red_triangle_down:ball tree是为了==克服kd树高维度失效而发明的，其构造过程是以质心C和半径r分割样本空间==，每个节点 是一个超球体

---

### 步骤分析

1.获取数据集

2.数据基本处理

3.特征工程

4.机器学习(模型训练)

5.模型评估

1. ```python
   '''
   1.获取数据集
   2.数据基本处理
   3.特征工程
   4.机器学习(模型训练)
   5.模型评估
   '''
   from sklearn.datasets import load_iris               #导入数据
   from sklearn.model_selection import train_test_split #模型选择
   from sklearn.preprocessing import StandardScaler     #标准化
   from sklearn.neighbors import KNeighborsClassifier   #机器学习
    
   #1.获取数据集
   iris=load_iris()
    
   #2.数据基础处理
   #2.1数据分割
   # 训练集的特征值x_train 测试集的特征值x_test 训练集的目标值y_train 测试集的目标值y_test
   x_train,x_test,y_train,y_test=train_test_split(iris.data,iris.target,random_state=22,test_size=0.2)
    
   #3.特征工程 标准化
   #3.1实例化一个转换器
   transfer=StandardScaler()
   #3.2调用fit_trainform方法
   x_train=transfer.fit_transform(x_train)
   x_test=transfer.fit_transform(x_test)
    
   #4.机器学习（模拟训练）
   #4.1实例化一个估计器
   estimator=KNeighborsClassifier(n_neighbors=5)
   #4.2模型训练
   estimator.fit(x_train,y_train)
    
   #5模型训练
   #5.1输出预测值
   y_pre=estimator.predict(x_test)
   print("预测值是:\n",y_pre)
   print("预测值和真实值对比:\n",y_pre==y_test)
    
   #5.2输出准确率
   ret=estimator.score(x_test,y_test)
   print("准确率是:\n",ret)
    
   '''
   预测值是:
    [0 2 1 1 1 1 1 1 1 0 2 1 2 2 0 2 1 1 1 1 0 2 0 1 1 0 1 1 2 1]
   预测值和真实值对比:
    [ True  True  True False  True  True  True False  True  True  True  True
     True  True  True  True  True  True False  True  True  True  True  True
    False  True False False  True False]
   准确率是:
    0.7666666666666667
   进程已结束,退出代码0
   '''
   ```


## 1.9  交叉验证和网格搜索

### 学习目标

​    知道交叉验证、⽹格搜索的概念

​    会使⽤交叉验证、⽹格搜索优化训练模型

---

### 1.9.1  什么是交叉验证validation)

**交叉验证**：将拿到的==训练数据==，==分为训练和验证集==。以下图为例：将训练数据分成4份，其中⼀份作为验证集。然后经过4次(组)的测试，每次都更换不同的验证集。即得到4组模型的结果，取平均值作为最终结果。⼜称4折交叉验证。



#### 分析

我们之前知道数据分为训练集和测试集，但是**为了让从训练得到模型结果更加准确。**做以下处理训练集：**训练集+验证集**

测试集：测试集

****

####   **为什么需要交叉验证**？

**1.交叉验证⽬的**：**为了让被评估的模型==更加准确可信不能提高准确率==**

**问题：这个只是让被评估的模型更加准确可信，那么怎么选择或者调优参数呢？**

---

### 1.9.2什么是⽹格搜索(Grid Search)

通常情况下，**有很多参数是需要==⼿动指定的（如k-近邻算法中的K值），这种叫超参数==**。但是⼿动过程繁杂，所以需要对模型预设⼏种超参数组合。==每组超参数都采⽤交叉验证来进⾏评估，最后选出最优参数组合建⽴模型。==**可以调高精确度**

![img](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/wps6A60.tmp.png) 

---

### 1.9.3  交叉验证，**网格搜索**（模型选择与调优）API

**APIsklearn.model_selection.GridSearchCV(estimator,param_grid=None,cv=None)**

​	**对估计器的指定参数值进行详尽搜索**
​	**estimator**:估计器对象
​	**param_grid**：估计器参数
​	**cv**:指定几折交叉验证

​	**fit()**：输入训练数据
​	**score()**：模型的准确率

**结果分析**：
	最佳参数:**best_params_**
	最佳结果:**best_score_**
	最佳估计器:**best_estimators_**
	交叉验证结果:**cv_results_**

 **模型的评估**
**1.直接对比预测值与真实值**
   	y_predict=estimator.predict(x_test)
   	print("y_predict:\n",y_predict)
   	print("直接比对真实值与预测值:\n",y_test==y_predict)

**2.计算准确率**
   	score=estimator.score(x_test,y_test)
   	print("准确率:\n",score)

**3.最佳参数**
  	 print("最佳参数:\n",estimator.best_params_)
  	 print("最佳结果:\n",estimator.best_score_)
  	 print("最佳估计器:\n",estimator.best_estimator_)
   	print("交叉验证结果:\n",estimator.cv_results_) 

---

### 1.9.4 案例—鸢尾花增加K值调优

 **使⽤GridSearchCV构建估计器**

GridSearchCV的名字其实可以拆分为两部分，**GridSearch和CV**，即网格搜索和交叉验证。
这两个名字都非常好理解。**网格搜索，搜索的是参数**，即在指定的参数范围内，
按步长依次调整参数，利用调整的参数训练学习器，从所有的参数中找到在验证集上精度最高的参数，
这其实是一个训练和比较的过程。

![](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/%E4%BA%A4%E5%8F%89%E9%AA%8C%E8%AF%81.png)

```python
from sklearn.model_selection import GridSearchCV

def knn_iris_gscv():
    """
    用KNN算法对鸢尾花进行分类，添加网格搜索和交叉验证
    :return:
    """
    # 1）获取数据
    iris = load_iris()
    # 2）划分数据集
    x_train,x_test,y_train,y_test = train_test_split(iris.data, iris.target, random_state=6)
    # 3）特征工程：标准化
    transfer = StandardScaler()
    x_train = transfer.fit_transform(x_train)
    # 注意：这里的测试集采用的是训练集中的平均值和标准差，所以采用的是 transform，而不是上面的 fit_transform
    x_test = transfer.transform(x_test)
    # 4）KNN算法预估器
    estimator = KNeighborsClassifier()
    # 加入网格搜索和交叉验证
    # 参数准备
    param_dict = {"n_neighbors":[1,3,5,7,9,11]}
    estimator = GridSearchCV(estimator, param_grid=param_dict, cv=10)
    estimator.fit(x_train, y_train) # x_train 是训练的数据，y_train 是数据对应的标签. 最终会得到训练模型
    # 5）模型评估
    # 方法一：直接对比真实值和预测值
    y_predict = estimator.predict(x_test)
    print("y_predict:\n", y_predict)
    print("直接对比真实值和预测值：\n", y_test == y_predict)
    # 方法二：计算准确率
    score = estimator.score(x_test, y_test)
    print("准确率为：\n", score)

    # 最佳参数：best_params_
    print("最佳参数：\n", estimator.best_params_)
    # 最佳结果：best_score_
    print("最佳结果：\n", estimator.best_score_)
    # 最佳估计器：best_estimator_
    print("最佳估计器：\n", estimator.best_estimator_)
    # 交叉验证结果：cv_result_
    print("交叉验证结果：\n", estimator.cv_results_)
    return None
knn_iris_gscv()
```

**然后进⾏评估查看最终选择的结果和交叉验证的结果**

![1638343635765](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1638343635765.png)

![1638343664333](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1638343664333.png)

 



## 1.10  案例三：预测facebook签到位置

### 项目描述

![](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/%E9%A2%84%E6%B5%8B%E7%AD%BE%E5%88%B0%E4%BD%8D%E7%BD%AE.png)

​		本次比赛的目的是预测一个人将要签到的地方。 为了本次比赛，Facebook创建了一个虚拟世界，其中包括10公里*10公里共100平方公里的约10万个地方。 对于给定的坐标集，您的任务将根**据用户的位置，准确性和时间戳等预测用户下一次的签到位置。**数据被制作成类似于来自移动设备的位置数据。 请注意：您只能使用提供的数据进行预测。

**数据集获取：**

**链接：https://pan.baidu.com/s/1gLyjsoWfHJpuamSr7HjuVA?pwd=1gin** 
**提取码：1gin**

### 数据集介绍

![1638344822836](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1638344822836.png)

```python
文件说明train.csv, test.csv
  row id：签入事件的id
  x y：坐标
  accuracy: 准确度，定位精度
  time: 时间戳
  place_id: 签到的位置，这也是你需要预测的内容
```

​		本例根据地点坐标和签到时间等特征来训练模型，最终得到目标地点的ID。训练集与测试集比例为8：2。

引入[python](https://so.csdn.net/so/search?from=pc_blog_highlight&q=python)库：

```python
import pandas as pd
from sklearn.model_selection import train_test_split,GridSearchCV
from sklearn.preprocessing import StandardScaler
from sklearn.neighbors import KNeighborsClassifier

# 1、获取数据集
facebook = pd.read_csv('E:\\train.csv')

```

​	在进行数据模型训练时，首先要进行数据预处理

​	缩小数据范围：因为数据集有2000W+条数据，程序跑起来会非常慢，因此适当缩小数据范围，如果电脑配置够或者租了服务器请随意
选择时间特征：数据中的时间分离出day，hour，weekend去掉签到较少的地方：剔除意义不大的特殊地点，减少过拟合确定特征值和目标值分割数据集

```python
# 2.基本数据处理
# 2.1 缩小数据范围
facebook_data = facebook.query("x>5.0 & x<6 & y>5.0 & y<6.0")   #选择（2,2.5）这一范围的数据，使用query
# 2.2 选择时间特征
time = pd.to_datetime(facebook_data["time"], unit="s")  #提取时间
time = pd.DatetimeIndex(time)
facebook_data["day"] = time.day  #加一列day
facebook_data["hour"] = time.hour  #加一列hour
facebook_data["weekday"] = time.weekday   #加一列weekday
# 2.3 去掉签到较少的地方
place_count = facebook_data.groupby("place_id").count()   #分组聚类，按数目聚类
place_count = place_count[place_count["row_id"]>3]       #选择签到大于3的
facebook_data = facebook_data[facebook_data["place_id"].isin(place_count.index)]   #传递数据
#facebook_data.shape()
# 2.4 确定特征值和目标值
x = facebook_data[["x", "y", "accuracy", "day", "hour", "weekday"]]  #特征值
y = facebook_data["place_id"]     #目标值
# 2.5 分割数据集
x_train, x_test, y_train, y_test = train_test_split(x, y, random_state=22)

# 3.特征工程--特征预处理(标准化)
# 3.1 实例化一个转换器
transfer = StandardScaler()
# 3.2 调用fit_transform
x_train = transfer.fit_transform(x_train)   #特征训练集
x_test = transfer.fit_transform(x_test)     #特征测试集
# 4.机器学习--knn+cv
# 4.1 实例化一个估计器
estimator = KNeighborsClassifier()
# 4.2 调用gridsearchCV
param_grid = {"n_neighbors": [1, 3, 5, 7, 9]}
estimator = GridSearchCV(estimator, param_grid=param_grid, cv=5)
# 4.3 模型训练
estimator.fit(x_train, y_train)
```

​	交叉验证：将拿到的训练数据，分为训练和验证集。以下图为例：将数据分成4份，其中一份作为验证集。然后经过4次(组)的测试，每次都更换不同的验证集。即得到4组模型的结果，取平均值作为最终结果。又称4折交叉验证。本实例cv=5，则为5折交叉验证。

![image-20220904150728552](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/image-20220904150728552.png)

```python
# 5.模型评估
# 5.1 基本评估方式
score = estimator.score(x_test, y_test)
print("最后预测的准确率为:\n", score)

y_predict = estimator.predict(x_test)
print("最后的预测值为:\n", y_predict)
print("预测值和真实值的对比情况:\n", y_predict == y_test)

# 5.2 使用交叉验证后的评估方式
print("在交叉验证中验证的最好结果:\n", estimator.best_score_)
print("最好的参数模型:\n", estimator.best_estimator_)
print("每次交叉验证后的验证集准确率结果和训练集准确率结果:\n",estimator.cv_results_)
```

### 总体代码

<img src="https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1638640034454.png" alt="1638640034454" style="zoom:150%;" />

![1638640069682](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1638640069682.png)

```python
import pandas as pd
from sklearn.model_selection import train_test_split , GridSearchCV  #交叉验证 网格搜索
from sklearn.preprocessing import StandardScaler                     #特征预处理
from sklearn.neighbors import KNeighborsClassifier                   #机器学习

# 导入数据
facebook = pd.read_csv('train.csv')#将文件放入与源代码相同的文件夹下

# 2.1 缩小数据范围
facebook_data = facebook.query("x>2.0 & x<2.5 & y>2.0 & y<2.5")

# 2.2 选择时间特征
time = pd.to_datetime(facebook_data["time"], unit="s")  # 提取时间使用pd.to_datetime() 将以上第一列的time(时间戳)的值转化成datetime。
time = pd.DatetimeIndex(time)
facebook_data["day"] = time.day          # 加一列day
facebook_data["hour"] = time.hour        # 加一列hour
facebook_data["weekday"] = time.weekday  # 加一列weekday
'''
 unit=“s”. 解释: unix的timestamp(时间戳)是从1970年1月1日（UTC/GMT的午夜）开始所经过的秒数, 
 即timestamp的单位为s，所以在将其转换成datetime时，使用的是单位“秒”。这里的参数unit使用的是s，
 而不是毫秒或微秒等其他时间单位。
'''


# 2.3 去掉签到较少的地方
place_count = facebook_data.groupby("place_id").count()                           # 分组聚类，按数目聚类
place_count = place_count[place_count["row_id"]>3]                                # 择签到大于3的
facebook_data = facebook_data[facebook_data["place_id"].isin(place_count.index)]  # 传递数据

# 2.4 确定特征值和目标值
x = facebook_data[["x", "y", "accuracy", "day", "hour", "weekday"]]  # 特征值
y = facebook_data["place_id"]                                        # 目标值


# 2.5 分割数据集
x_train, x_test, y_train, y_test = train_test_split(x, y, random_state=22)
# x_train是训练数据
# x_text是测试数据

# y_test数据具有测试数据的类别标签
# y_train测试出的标签

# 3.特征工程--特征预处理(标准化)
# 3.1 实例化一个转换器
transfer = StandardScaler()

# 3.2 调用fit_transform
x_train = transfer.fit_transform(x_train)  # 特征训练集
x_test = transfer.fit_transform(x_test)    # 特征测试集

# 4.机器学习--knn+cv
# 4.1 实例化一个估计器
estimator = KNeighborsClassifier()

# 4.2 调用GridSearchCV进行网格搜索和交叉验证
param_grid = {"n_neighbors": [1, 3, 5, 7, 9]}
estimator = GridSearchCV(estimator, param_grid=param_grid, cv=5)

'''
交叉验证：将拿到的训练数据，分为训练和验证集。
以将数据分成4份，其中一份作为验证集。然后经过4次(组)的测试，
每次都更换不同的验证集。即得到4组模型的结果，取平均值作为最终结果。
又称4折交叉验证。本实例cv=5，则为5折交叉验证。

GridSearchCV的名字其实可以拆分为两部分，GridSearch和CV，即网格搜索和交叉验证。
这两个名字都非常好理解。网格搜索，搜索的是参数，即在指定的参数范围内，
按步长依次调整参数，利用调整的参数训练学习器，从所有的参数中找到在验证集上精度最高的参数，
这其实是一个训练和比较的过程。
'''

# 4.3 模型训练
estimator.fit(x_train, y_train)

# 5.模型评估
# 5.1 基本评估方式
score = estimator.score(x_test, y_test)  # 对预测出的数据进行评分
print("最后预测的准确率为:\n", score)

y_predict = estimator.predict(x_test)
print("最后的预测值为:\n", y_predict)
print("预测值和真实值的对比情况:\n", y_predict == y_test)

# 5.2 使用交叉验证后的评估方式

print("在交叉验证中验证的最好结果:\n", estimator.best_score_)
print("最好的参数模型:\n", estimator.best_estimator_)
print("每次交叉验证后的验证集准确率结果和训练集准确率结果:\n", estimator.cv_results_)
```



输出结果

```
最后预测的准确率为:
 0.36567336567336567
 
最后的预测值为:
 [9983648790 6329243787 9674001925 ... 2990018952 4830766946 7065571836]
 
预测值和真实值的对比情况:
 24703810     True
19445902    False
18490063     True
7762709     False
6505956     False
            ...  
27632888    False
23367671    False
6692268      True
25834435    False
13319005    False
Name: place_id, Length: 17316, dtype: bool

在交叉验证中验证的最好结果:
 0.3546044971864908
 
最好的参数模型:
 KNeighborsClassifier(n_neighbors=1)
 
每次交叉验证后的验证集准确率结果和训练集准确率结果:
 {
  'mean_fit_time': array([0.05160828, 0.04941196, 0.05000744, 0.04960699, 0.04960599]),     'std_fit_time': array([0.00162514, 0.0008035 , 0.00062731, 0.00120043, 0.00102043]), 
 
  'mean_score_time': array([0.29386988, 0.32027264, 0.34508204, 0.3606843 , 0.39229727]),   'std_score_time': array([0.01042686, 0.00773276, 0.00829634, 0.0020539 , 0.01215737]), 
 
  'param_n_neighbors': masked_array(data=[1, 3, 5, 7, 9],
                     mask=[False, False, False, False, False],
 fill_value='?',
            dtype=object), 'params': [{'n_neighbors': 1}, {'n_neighbors': 3}, {'n_neighbors': 5}, {'n_neighbors': 7}, {'n_neighbors': 9}], 
            
            'split0_test_score': array([0.35948027, 0.34311838, 0.35235804, 0.35303176, 0.34927815]), 
            'split1_test_score': array([0.35466795, 0.34369586, 0.35563041, 0.35370549, 0.34821944]), 
            'split2_test_score': array([0.35524543, 0.34119346, 0.3506256 , 0.35129933, 0.34860443]), 
            'split3_test_score': array([0.3514294 , 0.34141881, 0.35681971, 0.35537588, 0.35075561]), 
            'split4_test_score': array([0.35219944, 0.34161132, 0.35152565, 0.34757917, 0.34132255]), 
            'mean_test_score': array([0.3546045 , 0.34220757, 0.35339188, 0.35219832, 0.34763604]), 
            'std_test_score': array([0.00283032, 0.00100506, 0.00240687, 0.00265359, 0.00327312]), 
            'rank_test_score': array([1, 5, 2, 3, 4])}

```

补充介绍：

| Jupyter数据的常用方法 | 输出结果                         |
| --------------------- | -------------------------------- |
| date.head()           | 数据的前几行                     |
| date.describe()       | 数据信息，最大值，最小值，平均值 |
| date.shaape           | 数据的行数和列数                 |
| date.query()          | 数据范围划分                     |

![1641790489883](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641790489883.png)

![1641790504946](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641790504946.png)

![1641790516273](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641790516273.png)

![1641790536266](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641790536266.png)

![1641790554145](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641790554145.png)

![1641790564145](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641790564145.png)

![1641790576320](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641790576320.png)

![1641790586424](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641790586424.png)

![1641790600936](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641790600936.png)

![1641790612131](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641790612131.png)

![1641790622055](https://pic-1313413291.cos.ap-nanjing.myqcloud.com/1641790622055.png)


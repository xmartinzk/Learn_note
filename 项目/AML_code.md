### aml模型文件夹

| 文件夹     | 备注                     |
| ---------- | ------------------------ |
| code_all   | 完成功能代码包           |
| code_split | 具体功能模块拆分代码包   |
| code_tool  | 工具类                   |
| data       | 输入输出数据文件存放位置 |
| model      | 存放模型文件             |



### 建模流程

#### 特征工程

| 名称                      | **备注**                                                     |
| ------------------------- | ------------------------------------------------------------ |
| **相关代码：**            |                                                              |
| Get_Features.py           | aml模型特征工程同样类                                        |
| get_features_for_model.py | 建模前对数据库中取出的黑白数据进行特征工程，为建模生成特征数据 |
| get_features_for_pre.py   | 对测试集进行特征工程,为模型预测生产特征数据                  |
| **输入数据：**            |                                                              |
| train.csv                 | 已打标的黑白数据，label列为标签                              |
| **输出数据：**            |                                                              |
| train_feature.csv         | 训练集特征数据                                               |



#### 特征重要性排序

| 名称              | **备注**                                           |
| ----------------- | -------------------------------------------------- |
| **相关代码：**    |                                                    |
| features_order.py | 根据初步模型训练后的结果对特征进行重要性打分并排序 |
| **输入数据：**    |                                                    |
| train_feature.csv | 训练集特征数据                                     |
| **输出数据：**    |                                                    |
| feature_level.csv | 特征排序数据                                       |



#### 模型搭建

| 名称              | 备注           |
| ----------------- | -------------- |
| **相关代码**      |                |
| model.py          | 建模           |
| **输入数据**      |                |
| train_feature.csv | 训练集特征数据 |
| **输入数据**      |                |
| model.gbm         | 模型文件       |



#### 模型效果

| 名称              | 备注                       |
| ----------------- | -------------------------- |
| **相关代码**      |                            |
| model_score.py    | 模型评估代码，评价模型效果 |
| **输入数据**      |                            |
| train_feature.csv | 训练集特征数据             |
| model.gbm         | 模型文件，导入后可用于预测 |
|                   |                            |



#### 模型预测

| 名称             | 备注                       |
| ---------------- | -------------------------- |
| **相关代码**     |                            |
| pred.py          | 预测                       |
| **输入数据**     |                            |
| test_feature.csv | 测试集特征数据             |
| model.gbm        | 模型文件，导入后可用于预测 |
| **输出数据**     |                            |
| pre.csv          | 预测结果                   |



### 模型重要代码说明

#### 特征重要性排序

###### features_order.py

```python
import sys
import pandas as pd
from sklearn.model_selection import train_test_split,RandomizedSearchCV
import lightgbm as lgb

'''
使用sys.argv获取命令行输入的参数，将其作为文件路径，
通过pandas读取CSV格式的数据文件，将其存储在名为data的变量中。
'''
path=sys.argv
data=pd.read_csv(path[1])

'''
或通过固定路径读取
'''
# data=pd.read_csv("../data/train_feature.csv")
```

```python
'''
使用sklearn.model_selection中的train_test_split函数，
将数据集data按照7:3的比例分割成训练集和测试集。
其中，x_train和x_test分别为训练集和测试集的特征矩阵，
y_train和y_test分别为训练集和测试集的标签。
data.iloc[:, 2:]表示选取data数据集的第3列至最后一列作为特征矩阵，
data[['label']]表示选取data数据集的label列作为标签。
'''
x_train,x_test,y_train,y_test=train_test_split(data.iloc[:,2:],data[['label']],test_size=0.3,random_state=1)
```

```python
'''
定义了一个字典search_params，包含了待优化的超参数及其范围。
num_leaves、n_estimators和min_data_in_leaf分别表示决策树的叶子节点数、弱学习器数量和叶子节点最少包含的样本数,learning_rate则表示学习率
'''
search_params={
    'num_leaves':range(200,500,20),
    'n_estimators':range(200,500,20),
    'min_data_in_leaf':range(100,200,20),
    'learning_rate':[0.01,0.05,0.1,0.2,0.3]
}
```

```python
'''
定义了一个字典other_params，包含了模型的其他超参数。
具体来说，objective表示模型的目标函数为二分类问题；
boosting_type表示使用GOSS算法提升模型性能；
max_depth表示每棵决策树的最大深度为4；
is_unbalance则表示数据不均衡。
'''
other_params={
    'objecttive':'binary',
    'boosting_type':'goss',
    'max_depth':4,
    'is_unbalance':'True'
}
```

```python
'''
这段代码使用lightgbm模型库中的LGBMClassifier函数，
创建了一个基础模型gbm，并使用随机搜索法（RandomizedSearchCV）对其超参数进行优化。
n_iter=30表示随机搜索迭代30次；
scoring='precision'表示评价指标为精确率；
cv=3表示使用3折交叉验证。最后，将最优超参数打印输出。
'''
gbm=lgb.LGBMClassifier(**other_params)
optimized_GBM=RandomizedSearchCV(gbm,search_params,n_iter=30,scoring='precision',cv=3,n_jobs=12)
optimized_GBM.fit(x_train,y_train)
optimized_GBM.best_params_
```

```python
'''
使用字典params将基础模型的超参数与最优超参数合并，
并使用LGBMClassifier函数创建最优模型best_gbm。
然后，使用fit函数将训练集x_train和y_train输入到最优模型中进行训练，生成模型model。
'''
params=dict(other_params,**optimized_GBM.best_params_)
best_gbm=lgb.LGBMClassifier(**params)
model=best_gbm.fit(x_train,y_train)
```

```python
'''
创建一个名为feature_importance_pd的DataFrame对象，
其中col_name列为特征名称，importance列为对应特征的重要性值。
然后，将训练好的模型model中的特征重要性值按从大到小排序，
并将结果保存在feature_importance_pd中。
最后，将feature_importance_pd保存为CSV格式的文件，存储在../data/feature_level.csv路径下。
'''
feature_importance_pd=pd.DataFrame()
feature_importance_pd['col_name']=x_train.columns
feature_importance_pd['importance']=model.feature_importances_
feature_importance_pd.sort_values('importance',ascending=False)

feature_importance_pd.to_csv('../data/feature_level.csv',index=False)
```

#### 模型搭建

###### model.py

```python
'''
pandas 库是用来处理数据的常用库；
sklearn 库是机器学习常用的库，包含了许多模型和模型评估方法；
lightgbm 是一个梯度提升树模型的实现库，它是一个高效的、快速的、分布式的梯度提升框架，被广泛应用于各类数据挖掘和机器学习任务；
joblib 库则是 Python 的一个用于在本地保存和加载大量 Python 对象的库，适用于需要存储大量的 Python 对象，如机器学习中的模型。
'''

import pandas as pd
from sklearn.model_selection import train_test_split, RandomizedSearchCV
import lightgbm as lgb
import joblib
```

```python
'''
读取了一个 csv 文件，文件名为 "../data/train_feature.csv"，其中 "../" 表示当前目录的上一级目录。将读取到的数据存储到一个 DataFrame 类型的变量 data 中。
'''
data = pd.read_csv("../data/train_feature.csv")
```

```python
'''
将数据集分为训练集和测试集。train_test_split() 方法是 sklearn 库中的一个函数，它可以把数据集划分为训练集和测试集，常用的参数有：
test_size：测试集占总数据集的比例；
random_state：随机数生成器的种子，用于控制数据集的随机划分。
其中，data.iloc[:, 2:] 表示从第 2 列开始到最后一列，即剔除了前两列，作为模型的输入特征，存储在 x_train 和 x_test 中；data[['label']] 则表示取 'label' 这一列，作为模型的输出结果，存储在 y_train 和 y_test 中。
'''
x_train, x_test, y_train, y_test = train_test_split(data.iloc[:, 2:], data[['label']], test_size=0.3, random_state=1)
```

```python
'''
定义一个字典search_params，其中包含了要进行随机搜索的参数范围:
'num_leaves': 代表LightGBM模型中的叶子节点数量，范围从200到500，步长为20。
'n_estimators': 代表LightGBM模型中的基学习器的数量，范围同样从200到500，步长为20。
'min_data_in_leaf': 代表每个叶子节点上的最小数据量，范围从100到200，步长为20。
'learning_rate': 代表学习率，控制每次迭代中更新权重时的步长大小，设定了5个不同的值：0.01, 0.05, 0.1, 0.2, 0.3。
在后续的随机搜索过程中，模型会在这些参数的范围内随机选择不同的组合，以寻找最优的参数组合。
'''
search_params = {
    'num_leaves': range(200, 500, 20),
    'n_estimators': range(200, 500, 20),
    'min_data_in_leaf': range(100, 200, 20),
    'learning_rate': [0.01, 0.05, 0.1, 0.2, 0.3]
}
```

```python
'''
定义一个字典变量 other_params，其中包含了一些LightGBM分类器的超参数:
objective: 模型优化的目标函数。在这里，我们使用的是二元分类问题，所以我们设置为binary。
boosting_type: 梯度提升树的类型。在这里，我们使用了LightGBM中的GOSS算法，这是一种加速梯度提升树训练的技术。
max_depth: 梯度提升树的最大深度。这个参数决定了树的层数，它的取值一般不宜过大，避免过拟合。
is_unbalance: 样本是否不平衡。当样本不平衡时，我们可以通过将is_unbalance设为True来调整分类器的参数，从而提高预测准确率。
这些超参数的选择需要根据具体的问题进行调整，可以通过交叉验证等方式进行调优
'''
other_params={
    'objecttive':'binary',
    'boosting_type':'goss',
    'max_depth':4,
    'is_unbalance':'True'
} 
```

```python
'''
创建LGBMClassifier类的对象gbm，其中传入的参数为**other_params，表示使用其他参数对模型进行配置，如目标函数为二元分类、使用goss提升算法、树的最大深度为4，以及类别是否不平衡
创建一个RandomizedSearchCV对象optimized_GBM，其中传入的参数为gbm，表示对gbm对象进行随机搜索；search_params，表示搜索空间，其中包括num_leaves、n_estimators、min_data_in_leaf和learning_rate等模型参数的范围；n_iter表示搜索的迭代次数；scoring表示评价指标，这里选择精确度；cv表示交叉验证的折数；n_jobs表示使用的CPU核心数。
'''
gbm=lgb.LGBMClassifier(**other_params)
optimized_GBM=RandomizedSearchCV(gbm,search_params,n_iter=30,scoring='precision',cv=3,n_jobs=12)
```

```python
'''
使用x_train和y_train训练optimized_GBM模型
并通过optimized_GBM.best_params_获取最佳的参数组合
将other_params和最佳参数组合合并为一个字典
并将其作为参数传入一个新的LGBMClassifier类的对象best_gbm中。
最后，使用x_train和y_train训练best_gbm模型
并将其保存为../model/model.gbm文件。
'''
optimized_GBM.fit(x_train,y_train)
optimized_GBM.best_params_

params=dict(other_params,**optimized_GBM.best_params_)
best_gbm=lgb.LGBMClassifier(**params)
model=best_gbm.fit(x_train,y_train)

joblib.dump(model,'../model/model.gbm')
```

#### 模型效果

###### model_score.py

```python
'''
导入`joblib`模块，用于模型的保存和加载。
导入`pandas`模块，用于数据读取和处理。
导入`sklearn.metrics`相关函数，用于模型评估指标的计算
'''
import joblib
import pandas as pd
from sklearn.metrics import roc_auc_score, roc_curve, recall_score, f1_score, log_loss, precision_score
```

```python
'''
使用`pandas`读取名为`train_feature.csv`的csv格式文件，并将数据存储在`data`变量中。
使用`joblib`模块中的`load`函数加载名为`model.gbm`的模型，并将其存储在`model`变量中。
'''
data=pd.read_csv("../data/train_feature.csv")
model=joblib.load('../model/model.gbm')
```

```python
'''
提取`data`中除第一列和最后一列以外的所有列，作为测试数据的特征数据，存储在`x_test`变量中。
提取`data`中最后一列作为测试数据的标签数据，存储在`y_test`变量中。
'''
x_test=data.iloc[:,1:-1]
y_test=data.iloc[:,-1]
```

```python
'''
使用训练好的模型`model`对测试数据的特征数据`x_test`进行预测，并将预测结果存储在`y_pred`变量中。
使用训练好的模型`model`对测试数据的特征数据`x_test`进行概率预测，并将预测结果存储在`y_pred_p`变量中。
计算ROC曲线上各个点的假阳性率`fpr`、真阳性率`tpr`和阈值`ths`，其中`y_pred_p[:,1]`表示预测标签为1的概率。
计算ROC曲线上的最大距离。
'''
y_pred=model.predict(x_test)
y_pred_p=model.predict_proba(x_test)
fpr,tpr,ths=roc_curve(y_test,y_pred_p[:,1])
max(abs(fpr-tpr))
```

```python
'''
输出模型的评估结果，包括测试精度、ROC曲线下面积、对数损失、F1分数、召回率和精度。
'''
print('test acc=%.3f,roc=%.3f,logloss=%.3f,f1score=%.3f,recall=%.3f,preci=%.3f'
      %(model.score(x_test,y_test),roc_auc_score(y_test,y_pred_p[:,1]),log_loss(y_test,y_pred_p[:,1]),
        f1_score(y_test,y_pred),recall_score(y_test,y_pred),precision_score(y_test,y_pred)))
```

#### 模型预测

###### pred.py

```python
'''
导入需要用到的库，包括 sys、joblib、pandas 和 warnings。
sys 库用于接收输入参数，
joblib 库用于加载模型，
pandas 库用于读取测试集特征数据和测试集数据，
warnings 库用于忽略警告信息。
接着分别读取测试集特征数据、测试集数据和模型。
'''
import sys
import joblib
import pandas as pd
import warnings
warnings.filterwarnings('ignore')
# os.environb['NUMEXPR_MAX_THREADS']='8'

feature_pd=pd.read_csv('../data/test_feature.csv')
in_data=pd.read_csv("../data/test.csv",sep=',',dtype=str)
model=joblib.load('../model/model.gbm')

```

```python
'''
对测试集进行预测，生成预测结果。使用 model.predict_proba() 函数对测试集特征数据进行预测，生成概率结果。
这里取概率结果的第二列作为预测结果（因为第二列是正例的概率）。
接着给测试集数据添加一个新的列 partyid_casedate，用于后续数据合并。
'''
feature_pd['预测风险']=model.predict_proba(feature_pd.iloc[:,1:])[:,1]
  
in_data['partyid_casedate']=in_data['case_date']+'_'+in_data['party_id']
data=in_data
```

```python
data_je=data.groupby('partyid_casedate')['cnt_amt'].sum().rename('交易总额').reset_index()
data_cnt=data.groupby('partyid_casedate')['cnt_amt'].count().rename('交易总笔数').reset_index()
data_je_in=data[data['receive_pay_cd']==1].groupby('partyid_casedate')['cnt_amt'].sum().rename('转入总额').reset_index()
data_je_out=data[data['receive_pay_cd']==2].groupby('partyid_casedate')['cnt_amt'].sum().rename('转出总额').reset_index()
data_cnt_in=data[data['receive_pay_cd']==1].groupby('partyid_casedate')['cnt_amt'].count().rename('转入总笔数').reset_index()
data_cnt_out=data[data['receive_pay_cd']==2].groupby('partyid_casedate')['cnt_amt'].count().rename('转出总笔数').reset_index()

data_opcnt=data[['partyid_casedate','opp_acct_num']]\
    .drop_duplicates()\
    .groupby(['partyid_casedate'])['opp_acct_num']\
    .count()\
    .rename('总交易对手数')\
    .reset_index()

data_opcnt_in=data[data['receive_pay_cd']==1][['partyid_casedate','opp_acct_num']]\
    .drop_duplicates()\
    .groupby(['partyid_casedate'])['opp_acct_num']\
    .count()\
    .rename('转入交易对手数')\
    .reset_index()

data_opcnt_out=data[data['receive_pay_cd']==2][['partyid_casedate','opp_acct_num']]\
    .drop_duplicates()\
    .groupby(['partyid_casedate'])['opp_acct_num']\
    .count()\
    .rename('转出交易对手数')\
    .reset_index()

data_staits=data_je.merge(data_cnt)\
    .merge(data_je_in,how='left')\
    .merge(data_je_out,how='left')\
    .merge(data_cnt_in,how='left')\
    .merge(data_cnt_out,how='left')\
    .merge(data_opcnt,how='left')\
    .merge(data_opcnt_in,how='left')\
    .merge(data_opcnt_out,how='left')

feature_pd=pd.merge(feature_pd[['partyid_casedate','预测风险']],data_staits)

feature_pd.to_csv('../data/pre.csv',index=False)
```


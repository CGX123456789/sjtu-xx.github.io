---
title: kaggle笔记-1(数据处理)
toc: true
date: 2020-01-30 10:37:45
tags: [kaggle]
categories:
- 机器学习
---

# 分类的分析
<!--more-->
1. 获得数据
2. 分析数据
  - 类别数据
    - 等级数据
    - 分类数据
  - 连续数据
  - 字母/数字/混合数据
4. 通过透视表pivot table分析数据
5. 通过图表可视化分析数据
  - 分析等级数据
  - 分析普通分类数据
  - 分析数据之间的关系
    - 相关性 `data.corr().nlargest(10,"result")["result]`
6. 处理数据
  - 选择特征
    - 缺失数据少
    - 具有代表性
  - 处理缺失数据
    - 随机数
    - 使用其他相关的参数进行估计（均值、中位数等）
  - 处理离群值
  - 根据实际情况添加特征
  - 删除特征
  - 添加新特征
7. 模型选择
  - 监督学习
    - 分类问题（逻辑回归，KNN，决策树，SVM，朴素贝叶斯，随机森林，感知机，人工神经网络，RVE(关联向量机)）

8. 模型训练
9. 模型结果分析

# 常用技术
## 特征提取和分析
https://www.kaggle.com/kashnitsky/topic-6-feature-engineering-and-feature-selection/notebook
- **文本**
`from sklearn.feature_extraction.text import CountVectorizer`
- **图像**
```python
from PIL import Image
from io import BytesIO
import requests
response = requests.get(url)
img = Image.open(BytesIO(response.content))
```
- **地理位置数据**
- **时间和日期**
- **时间序列**

## 特征转换
- 正则化和改变分布
`from sklearn.preprocessing import StandardScaler` 规范化
`from sklearn.preprocessing import MinMaxScaler` 将数据重新分布在min和max之间
`from scipy.stats import lognorm;
data = lognorm(s=1).rvs(1000)` 将对数正态分布转换为正态分布

## 特征选择
- 统计方法
方差小的认为数据的变化小
```python
from sklearn.feature_selection import VarianceThreshold
VarianceThreshold(.7).fit_transform(x_data_generated)
```
[其他统计学方法](https://scikit-learn.org/stable/modules/feature_selection.html#univariate-feature-selection)
- 通过模型选择
- GridSearch



## xgboost
　　Xgboost是Boosting算法的其中一种，Boosting算法的思想是将许多弱分类器集成在一起，形成一个强分类器。因为Xgboost是一种提升树模型，所以它是将许多树模型集成在一起，形成一个很强的分类器。而所用到的树模型则是CART回归树模型。
　　Xgboost是在GBDT的基础上进行改进，使之更强大，适用于更大范围。
　　Xgboost一般和sklearn一起使用，但是由于sklearn中没有集成Xgboost，所以才需要单独下载安装。

### xgboost教程
    分类问题和回归问题
    https://zhuanlan.zhihu.com/p/31182879



# 库
## pandas
1. 选择特定dtype对应的列
`[x  if data[c].dtype == np.dtype("O") else y for c in data]`
`categorical_features = df.select_dtypes(include="object")`
`numerical_features = df.select_dtypes(exclude="object")`

## seaborn
1. 分析相关性
`sns.heatmap(titanic.corr())`
<details>
<summary><b>heatmap参数</b></summary>
<p><b>cmap</b>：matplotlib 颜色条名称或者对象，或者是颜色列表，可选参数。 </p>
<p><b>center</b>：浮点数，可选参数。 </p>
<p><b>annot</b>:布尔值或者矩形数据，可选参数。(如果为True，则在每个热力图单元格中写入数据值。) </p>
<p><b>fmt</b>：字符串，可选参数。 </p>
<p><b>annot_kws</b>：字典或者键值对，可选参数。 </p>
<p><b>linewidths</b>：浮点数，可选参数。 </p>
<p><b>linecolor</b>：颜色，可选参数 </p>
<p><b>square</b>：布尔值，可选参数。(如果为True，则将坐标轴方向设置为“equal”，以使每个单元格为方形。) </p>
<p><b>xticklabels, yticklabels</b>：“auto”，布尔值，类列表值，或者整形数值，可选参数。 </p>
<p><b>mask</b>：布尔数组或者DataFrame数据，可选参数。 </p>
</details>

2. 检查正态和线性
:```python
#histogram and normal probability plot
sns.distplot(df_train['GrLivArea'], fit=norm);
fig = plt.figure()
res = stats.probplot(df_train['GrLivArea'], plot=plt)
```

## sklearn
1. 预处理
  - 将字符串类别转换为数字
  ``` python
  from sklearn.preprocessing import LabelEncoder
  la = LabelEncoder()
  result = la.fit_transform(data)
  ```
  - 自定义转换器
  > FeatureUnion将不同的转换器应用于整个输入数据，然后通过将它们合并来组合结果。
  > 另一方面，ColumnTransformer将不同的转换器应用于整个输入数据的不同子集，并再次将结果连接起来(比如说对每一列的数据进行处理)。

  ```python
  from sklearn.base import BaseEstimator, TransformerMixin
  class TitleSelector(BaseEstimator, TransformerMixin):
    def __init__( self):
        self.dict_title = {
            "Capt":       0,
            "Col":        0,
            "Major":      0,
            "Jonkheer":   1,
            "Don":        1,
            "Sir" :       1,
            "Dr":         0,
            "Rev":        0,
            "the Countess":1,
            "Dona":       1,
            "Mme":        2,
            "Mlle":       3,
            "Ms":         2,
            "Mr" :        4,
            "Mrs" :       2,
            "Miss" :      3,
            "Master" :    5,
            "Lady" :      1
        }
   
    def fit(self, X, y=None):
        return self 
    
    def transform( self, X, y=None):
        for i, name in enumerate(X["Name"]):
            for title in self.dict_title.keys():
                if title in name:
                    X["Name"][i] = self.dict_title[title]
                    break
        
            assert X["Name"][i] in self.dict_title.values()
        
        return X
    
name_transformer = Pipeline(steps=[
    ('name', TitleSelector()),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))
])
  ```
  **使用ColumnTransformer**  
  ```python
  num_cols = ["Age", "Fare", ]
  cat_cols = ["Pclass", "Sex", "SibSp", "Parch", "Ticket", "Cabin", "Embarked"]
  cols = num_cols + cat_cols + ["Name"]
  
  
  preprocessor = ColumnTransformer(transformers=[
      ('num', numerical_transformer, num_cols),
      ('name', name_transformer, ["Name"]),
      ('cat', categorical_transformer, cat_cols),
  ])
  
  X_train = preprocessor.fit_transform(df_train[cols])
  y_train = df_train["Survived"].values
  ```
  - 标准化、正则化
  ```python
  # 标准化：均值为0，方差为1
  from sklearn.preprocessing import scale,StandardScaler
  scaler = preprocessing.StandardScaler().fit(X)
  scaler = preprocessing.StandardScaler().fit_transform(X)
  # 限制范围
  from sklearn.preprocessing import MinMaxScaler
  preprocessing.MinMaxScaler().fit_transform(X)
  # 正则化:范数为1
  preprocessing.normalize(X, norm='l2')
  ```
2. 模型训练
  - pipeline
  - 超参数搜索
  ```python
   from sklearn.pipeline import Pipeline
   from sklearn.model_selection import GridSearchCV
   from sklearn.tree import DecisionTreeClassifier
   
   clf = Pipeline([("cls",AdaBoostClassifier(DecisionTreeClassifier(),algorithm="SAMME",n_estimators=600, learning_rate=0.7))])
   params = [{"cls__learning_rate":np.linspace(0.1,1,10)}]
   grid_search = GridSearchCV(clf,params,cv=3)
   
   grid_search.fit(X_train,Y_train)
   grid_search.best_score_
  ``` 
3. 模型结果
  ```python
  model.predict(x_test)
  model.score(X_train,y_train)
  ```  
4. 结果分析
   ```python
   from sklearn.model_selection import learning_curve
   N_train, val_train, val_test = learning_curve(pipeline,
                                                  X, y, train_sizes=train_sizes, cv=5,
                                                  scoring='roc_auc')
   ```

## tensorflow
1. 层
 - Dense：全连接层
 - Dropout：dropout层
 - BatchNormalization：批标准化层
 - conv2D：卷积层
 - Embedding：类似onehot编码处理，但是会避免稀疏性

2. 模型
``` python
from tensorflow.keras.layers import Dense, Dropout, BatchNormalization
from tensorflow.keras.models import Sequential
model = Sequential()
model.add(Dense(32, input_dim=858, activation='relu'))
model.add(Dropout(0.9))
model.add(Dense(32, activation='relu'))
model.add(BatchNormalization())
model.add(Dropout(0.9))
model.add(Dense(1, activation='sigmoid'))
model.compile(loss='binary_crossentropy', optimizer='adam', metrics=['accuracy'])
model.fit(X_train, y_train, epochs=1000, batch_size=8)
X_test = preprocessor.transform(df_test[cols])
y_pred = model.predict_classes(X_test)
```

`print(model.summary())`显示模型的详细信息


# 案例
   [泰坦尼克号1](https://blog.csdn.net/han_xiaoyang/article/details/49797143)
    

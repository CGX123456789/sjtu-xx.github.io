---
title: kaggle笔记-2（常见模型）
toc: true
date: 2020-02-06 15:11:56
tags: [深度学习,kaggle]
categories:
- 机器学习
---

# 常见的模型
<!--more-->
## 监督模型
### 线性回归
文本分类中线性模型具有很好的准确性。
对XOR问题线性模型难以区分
对于非线性问题，支持向量机往往结果更好

### 支持向量机

### 决策树
回归和分类树

### 集成学习方法
通过多个模型集成来获得最终结果
- 随机森林
- Adaboost
- xgboost

## 无监督模型
### 主成分分析
1. PCA
有线性约束
用法:
```python
from sklearn import decomposition
pca = decomposition.PCA(n_components=2)
pca.fit(data_x)
```


2. t-SNE
没有线性约束
```python
from sklean.manifold import TSNE
tsne = TSNE(random_state=20)
tsne.fit(data_x)
```

### 聚类算法
1. KMeans
簇个数的选择,选择inertia下降慢的簇个数
```python
from sklearn.cluster import KMeans
kmeans = KMeans(n_cluster=3).fit(X)
inertia = np.sqrt(kmeans.inertia_)
```
2. Affinity Propagation（近邻传播聚类）
不需要设置簇的个数，将每个数据视为一个节点，观察与其他节点的相似性。不断更新吸引度和归属度进行分析。

3. Spectral Clustering（谱聚类）
首先定义相似矩阵（用来确定两个数据之间的距离），该矩阵描述了一个以观测值为顶点，以观测值对间的估计相似度值为边权的完整图。对于上面定义的度量和二维观测，这是非常直观的-如果两个观测之间的边较短，则两个观测是相似的。我们想把图分解成两个子图，这样每个子图中的每个观察都与该子图中的另一个观察相似。

4. Agglomerative clustering（层次聚类）
将每个观测值赋给不同的簇，计算簇之间的距离，不断的合并最近邻的簇
```python
from scipy.cluster import hierarchy
from scipy.spatial.distance import pdist

X = np.zeros((150,2))

np.random.seed(seed=42)
X[:50, 0] = np.random.normal(loc=0.0, scale=.3, size=50)
X[:50, 1] = np.random.normal(loc=0.0, scale=.3, size=50)

X[50:100, 0] = np.random.normal(loc=2.0, scale=.5, size=50)
X[50:100, 1] = np.random.normal(loc=-1.0, scale=.2, size=50)

X[100:150, 0] = np.random.normal(loc=-1.0, scale=.2, size=50)
X[100:150, 1] = np.random.normal(loc=2.0, scale=.5, size=50)

# pdist will calculate the upper triangle of the pairwise distance matrix
distance_mat = pdist(X) 
# linkage — is an implementation if agglomerative algorithm
Z = hierarchy.linkage(distance_mat, 'single')
plt.figure(figsize=(10, 5))
dn = hierarchy.dendrogram(Z, color_threshold=0.5)
```
5. 聚类结果的判定
- Adjusted Rand Index (ARI)
- Adjusted Mutual Information (AMI)
- Homogeneity, completeness, V-measure
- Silhouette

6. 聚类算法的使用
```python
from sklearn import metrics
from sklearn import datasets
import pandas as pd
from sklearn.cluster import KMeans, AgglomerativeClustering, AffinityPropagation, SpectralClustering


data = datasets.load_digits()
X, y = data.data, data.target

algorithms = []
algorithms.append(KMeans(n_clusters=10, random_state=1))
algorithms.append(AffinityPropagation())
algorithms.append(SpectralClustering(n_clusters=10, random_state=1,
                                     affinity='nearest_neighbors'))
algorithms.append(AgglomerativeClustering(n_clusters=10))

data = []
for algo in algorithms:
    algo.fit(X)
    data.append(({
        'ARI': metrics.adjusted_rand_score(y, algo.labels_),
        'AMI': metrics.adjusted_mutual_info_score(y, algo.labels_,
                                                 average_method='arithmetic'),
        'Homogenity': metrics.homogeneity_score(y, algo.labels_),
        'Completeness': metrics.completeness_score(y, algo.labels_),
        'V-measure': metrics.v_measure_score(y, algo.labels_),
        'Silhouette': metrics.silhouette_score(X, algo.labels_)}))

results = pd.DataFrame(data=data, columns=['ARI', 'AMI', 'Homogenity',
                                           'Completeness', 'V-measure', 
                                           'Silhouette'],
                       index=['K-means', 'Affinity', 
                              'Spectral', 'Agglomerative'])

results
```

## 图像分类
### 普通卷积模型
两个卷积层+一个池化层

## 图像分割
### U-NET：用于图像分割的卷积神经网路
https://blog.csdn.net/Formlsl/article/details/80373200


---
title: 机器学习实战(url分类)
date: 2017-03-31 00:40:44
tags:
---

  
实际应用中，我们经常需要对集合进行分类，一方面可以进行统计，另一方面也可以用作去重。常见的例如**url分类**（本质上是字符串分类，不过又因为url的特殊性使其需要特别处理），**地址分类**（经纬度分类），**文章分类**（文章分类我们之后会介绍）。分类的其中一种思路是先把集合量化为几项特征值，使其可以计算出个体间的距离。然后通过分类算法（这里使用的是k-means算法）进行分类。

  这里举的例子是url分类，我们从爬虫获取到url，需要把它们分成不同的类别。我们先分析一个url：

    https://www.example.com/abc/123?id=1 

我们可以把url抽象成几个特征：

**http还是https，域名，目录，目录深度，参数，参数数量，域名在Alexa上的排名。域名在搜索引擎的收录数**

这一步非常重要，有些开发者往往只在乎算法的效率和实现，忽略了从需求角度去对数据进行分析和提取。就像理查德费曼所说的，“无论计算机多少神奇，你给它的是垃圾，它出来的也是垃圾”。

分析以上的url我们可以得到：

1. 访问安全性：https
2. 域名为：www.example.com
3. 目录：abc/123
4. 目录深度：三级
5. 参数：id=1
6. 参数数量：1个
7. Alexa排名：10000（非真实）
8. 搜索引擎收录数：1000（非真实）


我们根据需求给这8个特征赋予不同的权重

    Similarity = 域名 * 0.4 + 目录 * 0.2 + 目录深度 * 0.2 + 参数 * 0.1 + 参数数量 * 0.1 

在这里我们只使用5个特征，特征的数量对具体实现并没有影响。不过这里需要注意一个问题，在计算距离的时候我们可以使用**欧式距离（Euclidean Distance）**，**编辑距离（Edit Distance）**，**余弦相似性（Cosine Similarity）**。欧式距离公式如下：

在这里我们可以看到，不同的特征由于它的绝对值范围不同（例如域名一般会在20个字符串内，而Alexa排名却可以在一到几百万的区间），对最终距离影响的范围也不同。例如在所有url中Alexa排名最前的拍在第100位，最后为2000位。那么这两个url的距离因为排名这个特征被过度放大了。所以在计算距离之前，我们常常需要归一化数值，将特征值的取值范围局限在0到1或者-1到1之间，公式如下：

    newValue = (oldValue - min) / (max - min)

假如集合中一个url的排名为500，那么在计算过之后就变成

    newValue = (500 - 100) / (2000 - 100) = 0.21

之后可以计算url之间每个特征的编辑距离。

    https://www.example.com/abc/123?id=1
    https://www.example.com/abc/cdf/258?id=19

这里的编辑距离分别为，0，6，1，1，0，（这是归一化数值之前的编辑距离，实际应用需要先获得每个特征值的距离最大最小值，之后再把编辑距离转化为0到1之间）计算编辑距离的代码如下：


    import numpy as np
    
    def levenshtein(source, target):
        ''' 使用矩阵计算比其他方法快40% '''
        if len(source) = len(target).
        if len(target) == 0:
            return len(source)
    
        # We call tuple() to force strings to be used as sequences
        # ('c', 'a', 't', 's') - numpy uses them as values by default.
        source = np.array(tuple(source))
        target = np.array(tuple(target))
    
        # We use a dynamic programming algorithm, but with the
        # added optimization that we only need the last two rows
        # of the matrix.
        previous_row = np.arange(target.size + 1)
        for s in source:
            # Insertion (target grows longer than source):
            current_row = previous_row + 1
    
            # Substitution or matching:
            # Target and source items are aligned, and either
            # are different (cost of 1), or are the same (cost of 0).
            current_row[1:] = np.minimum(
                    current_row[1:],
                    np.add(previous_row[:-1], target != s))
    
            # Deletion (target grows shorter than source):
            current_row[1:] = np.minimum(
                    current_row[1:],
                    current_row[0:-1] + 1)
    
            previous_row = current_row
    
        return previous_row[-1]


其他实现方法可以参考[编辑距离](https://en.wikibooks.org/wiki/Algorithm_Implementation/Strings/Levenshtein_distance#Python)


知道怎么计算距离，就可以使用分类算法，简单的可以使用k-means。这里为了讲解，并不是用numpy库


    import random
    import functools
    from ml_logging import setup_logging
    
    
    setup_logging(__name__)
    
    
    def read_from_file(file_name, dimension_num, split_char):
        '''从文件读取数据
           file_name: 数据源文件名
           dimension_num: 选择的数据维度数量
           split_char: 数据分隔符
        '''
        def list_to_float(
                line, dimension_num=dimension_num, split_char=split_char):
            l = [float(x) for x in line.replace("\n", "").split(
                split_char)[:dimension_num]]
            return l
    
        try:
            with open(file_name, 'r') as data:
                # 若每行数据源为a1 a2 a3
                # 返回格式为float数值
                # [[a1, a2, a3], [b1, b2, b3], [c1, c2, c3]]
                dataset = [list_to_float(x) for x in data]
        except IOError:
            pass
        return dataset
    
    
    def cluster_random(dataset, chuster_num):
        '''随机选择质点
           dataset: 数据源
           chuster_num: 质点数量
        '''
        return random.sample(dataset, chuster_num)
    
    
    def cal_distance(a, b):
        '''计算两个数据源之间的欧几里得距离
           dataset: 数据源
           chuster_num: 质点数量
        '''
        return functools.reduce(
            lambda x, y: x+y, [(x - y) ** 2 for x, y in zip(a, b)]) ** 0.5
    
    
    def cal_multi(cluster_list, dataset):
        '''把每一个点分到最近的质心，返回分组后的情况
           cluster_list: 质心列表
           dataset: 数据源
        '''
        di = {}
        for i in dataset:
            ans = list(map(lambda x: cal_distance(i, x), cluster_list))
            cluster_index = ans.index(min(ans))
            di.setdefault(cluster_index, []).append(i)
            # di返回的格式应该如下：
            # di = {
            #          1: [[a1, a2], [c1, c2]],
            #          2: [[b1, b2], [d1, d2]]
            #      }
        return di
    
    
    def new_cluster(cluster_dict):
        '''获取新的聚簇点
           cluster_dict: 包含分组信息的字典
        '''
        def average(nums):
            return sum(nums)/len(nums)
    
        return list(
            map(lambda x: (average(n) for n in zip(*x.values())), cluster_dict))
    
    
    def main(file_name, dimension_num, split_char, k):
        # 读取数据
        dataset = read_from_file(file_name, dimension_num, split_char)
        # 选择初始的k个质点
        cluster_list = cluster_random(dataset, k)
        # 这是第一次分组
        cluster_before = False
        # 默认分组为空
        cluster_dict = {}
        while 1:
            if cluster_before:
                # 根据现有分组情况计算新的质心
                new_cluster(cluster_dict)
            else:
                cur_dict = cal_multi(cluster_list, dataset)
                cluster_before = True
                if cur_dict == cluster_dict:
                    break
                else:
                    cluster_dict = cur_dict
使用numpy实现可以参考[python的k-means实现](https://datasciencelab.wordpress.com/2013/12/12/clustering-with-k-means-in-python/)

这里使用k-means算法，太受随机起始点的影响，可以结合二分k-means和k-means++进行分类，使分类更加稳定。

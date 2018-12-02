---
layout: post
author: zhuojin
tags: 机器学习 推荐系统
---
论文地址：<a href="https://research.google.com/pubs/archive/45530.pdf">Deep Neural Networks for YouTube Recommendations</a>

<h3>一、背景介绍</h3>
Youtube是世界上最大规模的视频网站，其推荐系统每天为超过10亿的用户推荐个性化的视频内容。这篇文章介绍了Youtube的推荐算法以及架构细节。

推荐系统主要有三个难点：
- 大规模：用户和视频内容都是超大规模的。因此高效的学习算法以及鲁棒的在线服务必不可少。
- 新鲜度：对于像Youtube这样的大型视频网站，每秒都有大量新发布的视频，需要在历史内容和新鲜内容之间balance。同时，推荐系统必须及时捕捉用户的交互信息。
- 数据噪声：由于数据稀疏性和许多外部因素，用户的历史播放记录非常难以预测。推荐算法只能从充满噪声的数据中学习，而不是用户的真实满意度。

<h3>二、系统概览</h3>
Youtube的推荐系统主要分为候选集生成(candidate generation)和排序(ranking)两个模块。
在候选集生成阶段，系统会从百万量级的视频库中选出上百个与用户相关的视频。在排序阶段，算法对每一个item-user对打分，并以此生成一个最优的推荐序列。
这样的两层结构使得推荐系统可以从大规模的视频库中为用户推荐少量的个性化内容，并且可以通过其他手段生成候选集参与排序。
<img src="/assets/img/youtube_recsys/sys.png"/>

<h3>三、候选集生成</h3>
<h4>3.1 问题建模</h4>
<p>我们把推荐问题建模为一个超大规模的分类问题。即在时刻t，基于用户U和上下文C，从视频库V中预测用户在此时刻所观看的视频i。</p>
<img src="/assets/img/youtube_recsys/matching_formulation.png"/>
<p>对于这种有上百万个类别的分类问题，我们采用了负采样的技术（类似word2vec里所采用的negative sampling）。</p>
<h4>3.2 模型结构</h4>
<img src="/assets/img/youtube_recsys/matching_model.png"/>
<p>整个模型是包含三个ReLU隐层的DNN。输入是由用户浏览历史、搜索历史、人口统计学信息和其余上下文信息concat成的输入向量。其中浏览历史信息由video embedding的加权和表示，搜索信息由token embedding的加权和表示。</p>
<p>模型输出分为离线训练和线上预测两个部分。离线训练阶段输出层为一个softmax函数，输出3.1中所表达的概率。而线上利用user向量查询相关的item向量。线上服务的主要问题在于性能，可以采用一些近似最近邻的方法加快查找效率。</p>

<h3>四、排序</h3>
排序阶段的最重要的是精准地预估用户对视频的喜好程度。排序阶段面对的只是上百个相关的视频，因此我们可以使用更多更精细的特征来刻画视频以及用户与视频的关系。
<h4>4.1 模型结构</h4>
排序阶段的模型和候选集生成所采用的模型非常类似，不同的是，训练时模型最后一层是一个weighted LR层，而线上服务时激励函数用的是e^{x} 。
<img src="/assets/img/youtube_recsys/ranking_model.png"/>

<h3>五、总结</h3>
现在matching+ranking这样的两层架构几乎已经成为工业界的标配。但是这篇文章提到的一些特征处理方法，以及训练数据的选择，仍然都非常有借鉴意义，如example age的使用，无序的搜索输入，使用更广的数据源以及为每个用户生成固定数量训练样本等等。
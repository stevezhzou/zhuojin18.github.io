---
layout: post
author: zhuojin
tags: 机器学习 推荐系统
---
论文地址：<a href="https://research.google.com/pubs/archive/45530.pdf">Deep Neural Networks for YouTube Recommendations</a>

<h3>背景介绍</h3>
Youtube是世界上最大规模的视频网站，其推荐系统每天为超过10亿的用户推荐个性化的视频内容。这篇文章介绍了Youtube的推荐算法以及架构细节。

推荐系统主要有三个难点：
- 大规模：用户和视频内容都是超大规模的。因此高效的学习算法以及鲁棒的在线服务必不可少。
- 新鲜度：对于像Youtube这样的大型视频网站，每秒都有大量新发布的视频，需要在历史内容和新鲜内容之间balance。同时，推荐系统必须及时捕捉用户的交互信息。
- 数据噪声：由于数据稀疏性和许多外部因素，用户的历史播放记录非常难以预测。推荐算法只能从充满噪声的数据中学习，而不是用户的真实满意度。

<h3>系统概览</h3>
Youtube的推荐系统主要分为候选集生成(candidate generation)和排序（ranking）两个模块。
在候选集生成阶段，系统会从百万量级的视频库中选出上百个与用户相关的视频。在排序阶段，算法对每一个item-user对打分，并以此生成一个最优的推荐序列。
这样的两层结构使得推荐系统可以从大规模的视频库中为用户推荐少量的个性化内容，并且可以通过其他手段生成候选集参与排序。
<img src="/assets/img/youtube_recsys/sys.png"/>

<h3>候选集生成</h3>
我们把推荐问题建模为一个超大规模的分类问题。即在时刻t，基于用户U和上下文C，从视频库V中预测用户在此时刻所观看的视频i。
<img src="/assets/img/youtube_recsys/matching_formulation.png"/>
对于这种有上百万个类别的分类问题，我们采用了负采样的技术（类似word2vec里所采用的negative sampling）。
<h4>模型结构</h4>
<img src="/assets/img/youtube_recsys/matching_model.png"/>


<h3>排序</h3>


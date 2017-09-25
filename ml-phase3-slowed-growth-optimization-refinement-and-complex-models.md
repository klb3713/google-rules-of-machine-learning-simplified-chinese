# 缓慢增长、优化和复杂模型

有一些迹象表明，第二阶段已经达成。首先，月收入可能会缩减。你不得不在多个指标中做取舍：一个实验中，某些指标上涨，但是某些指标下跌。这就有意思了。因为收入上涨很难，模型就得更加复杂。警告：这章的内容更加像 blue-sky rules（Eric：意思是非普适的准则吧），我们见过很多团队度过了机器学习的第一和第二阶段的美好时光后，到了第三阶段就只能各显神通了。

## Rule 38 - 如果优化目标没对齐就别浪费时间去找新特征了

 当度量达到了瓶颈，团队开始面临老模型优化目标没有衡量到的那些问题。像刚在提到的，如果当前的算法目标没有涵盖产品目标，就得花时间去改变产品目标或者算法目标。比如，你去优化点击，点赞或者下载，但是是否决定上线还是依赖人工打分的时候。

## Rule 39 - 上线决策是长线产品目标的代理

 爱丽丝有一个优化下载预测模型的主意。她加了一个新特征，果然模型表现变好了。她做一个线上实验，安装率的确也涨了。但是当她去参加发布评审会时，有人指出 DAU（日活跃用户量）下降了5%。团队决定不发布这个心模型。爱丽丝觉得很沮丧，但是也意识到上线决策依赖很多条件，而只有一部分能被机器学习直接优化。现实世界不是"龙与地下城"游戏：没有一个单一的"hit points"来表明产品的健康度。团队需要从收集到的所有指标来判断系统的未来。他们需要关注沉浸度，1日 DAU，30日 DAU，收入和广告商的回报。在 A/B test 实验中的那些可以被度量的指标，只是真实长远目标的一个代理：用户满意度，用户增长，合作伙伴满意度，收入，甚至是那些你没发找到衡量指标的长远目标，比如产品的有用程度，质量好坏以及公司未来五年的持续繁荣。

** 只有所有指标都涨了（或者至少没跌），发布决策才容易做。** 如果有一个精致的机器学习模型，和一个土了吧唧的人工规则模型放在那给团队选，如果人工规则模型在指标上表现更好，当然应该选人工规则。更进一步，关于不同指标的重要度，其实也没有定论。比如下面这两个情况：

| 实验 |  日活跃用户数 | 收入/天 |
|------|-------------|--------|
| A     | 100w    | $400w  |
| B     | 200w    | $200w  |

 如果当前系统是 A，团队可能选择切换到 B。如果当前用的是 B，团队也能原因切换为 A。这么做看起来并不理性：但是很难讲这么切换会利大于弊，还是弊大于利，因此会带来很大的风险。每个指标都会带来一些风险，但是没有指标能够涵盖团队的终极问题，"五年后我的产品应该是什么样子？"

**相反，个人则倾向于追求那些他们可以直接优化的指标。** 大多数的机器学习工具也喜欢这样的设定。一个鼓捣出新特征的工程师，立刻就可以在这样的环境下进行产出。还有一种机器学习致力于解决这种问题，多目标学习。比如，人们可以形式化一个优先的满足问题，考虑多个指标，并且对这些指标的线性组合做优化。即便如此，也不是所有的指标都能作为机器学习的目标：一个文档被点击，一个 app 被下载，多半是因为用户看到了他。但是用户为啥来你的站点缺难解释得多。如何预测站点整体在未来的表现，是一个[AI­complete](https://en.wikipedia.org/wiki/AI-complete)问题，和计算机视觉以及自然语言处理一样难。

#### Rule 40 - Keep ensembles simple.
## Rule 40 - 模型组合 保持简单

 用原始特征直接进行排序的单一模型 debug 和理解起来都最简单。但是模型组合（用其他多个模型的输出做特征）可能效果更好。**为了简单起见，一个模型要莫是只用其他模型的输出做输入，要莫是拿原始特征的基础模型，别混用。** 如果上层模型没有和底层模型一起训练，那么组合的效果可能不会好。

 只用上层模型来组合底层模型的输出。可能这些组合模型的功能也很重要。比如某个 base 模型的提升，不应该降低组合模型的总体分数。另外，如果底层模型的输出语意是可解释的就最好不过了（比如校准？），这样底层模型的变化就不会造成上层模型的混乱。**另外，底层模型的概率输出，应该和总体组合的概率输出是正相关的（即同涨，同跌）。**

## Rule 41 - 当遭遇效果瓶颈，与其提炼旧特征，还不如去找哪些能带来信息量的新特征

 已经加了用户的人口统计学特征（比如性别、年龄等），已经加了文档中的词特征，也试过了 template exploration，也调了正则化。已经几个 q 没有1%以上的提升可以用来上线了。咋整？
You’ve added some demographic information about the user. You've added some information about the words in the document. You have gone through template exploration, and tuned the
regularization. You haven’t seen a launch with more than a 1% improvement in your key metrics in a few quarters. Now what?
It is time to start building the infrastructure for radically different features, such as the history of documents that this user has accessed in the last day, week, or year, or data from a different property. Use [wikidata](https://en.wikipedia.org/wiki/Wikidata) entities or something internal to your company (such as Google’s [knowledge graph](https://en.wikipedia.org/wiki/Knowledge_Graph)). Use deep learning. Start to adjust your expectations on how much return you expect on investment, and expand your efforts accordingly. As in any engineering project, you have to weigh the benefit of adding new features against the cost of increased complexity.

#### Rule 42 - Don't expect diversity, personalization, or relevance to be as correlated with popularity as you think they are.

Diversity in a set of content can mean many things, with the diversity of the source of the content being one of the most common. Personalization implies each user gets their own results. Relevance implies that the results for a particular query are more appropriate for that query than any other. Thus all three of these properties are defined as being different from the ordinary.
The problem is that the ordinary tends to be hard to beat.

Note that if your system is measuring clicks, time spent, watches, +1s, reshares, et cetera, you are measuring the popularity of the content. Teams sometimes try to learn a personal model with diversity. To personalize, they add features that would allow the system to personalize (some features representing the user’s interest) or diversify (features indicating if this document has any features in common with other documents returned, such as author or content), and find that those features get less weight (or sometimes a different sign) than they expect. This doesn’t mean that diversity, personalization, or relevance aren’t valuable.\* As pointed out in the previous rule, you can do post-­processing to increase diversity or relevance. If you see longer term objectives increase, then you can declare that diversity/relevance is valuable, aside from popularity. You can then either continue to use your post­-processing, or directly modify the objective based upon diversity or relevance.

<sup>[Google Research Blog - App Discovery With Google Play](https://research.googleblog.com/2016/12/app-discovery-with-google-play-part-2.html?m=1)

#### Rule 43 - Your friends tend to be the same across different products. Your interests tend not to be.

Teams at Google have gotten a lot of traction from taking a model predicting the closeness of a connection in one product, and having it work well on another. Your friends are who they are. On the other hand, I have watched several teams struggle with personalization features across product divides. Yes, it seems like it should work. For now, it doesn’t seem like it does. What has sometimes worked is using raw data from one property to predict behavior on another. Also, keep in mind that even knowing that a user has a history on another property can help. For instance, the presence of user activity on two products may be indicative in and of itself.

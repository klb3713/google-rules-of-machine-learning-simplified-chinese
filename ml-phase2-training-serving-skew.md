
# 训练和使用偏差

训练使用偏差是指模型在训练上的表现和在线上实际使用的表现不一致。这可能是以下原因带来的：
* 训练和线上，处理数据的方式不同
* 训练和线上，数据变了
* 模型和算法的反馈循环

在 Google 的实际机器学习应用中，我们发现这种训练和使用偏差的确对系统整体有负面影响。最好的解决办法是可以去监控这种偏差，以免系统和数据的变更引入更大偏差时候我们发现不了。

## Rule 29 - 让训练更接近使用的最佳方式是在线上把一堆特征用日志保存下来以便在训练时候使用

即使你没发每个样本都这么做，至少抽样来搞，这样你就可以调整训练和使用的一致性（见 Rule #37)。在 Google，这么做的团队往往会收获惊喜。YouTube 的首页业务采用这种日志化特征后，质量有重大改善，而且代码复杂度也下降了。在这片文章写就时，很多其他团队也在进行类似改造。

## Rule 30 - 给抽样后的数据乘上权重,不要直接扔掉！

当文件太多的时候，很可能你保留前12个文件，把剩下的13-99号文件丢了。这不对：因为在训练阶段直接扔文件在 Google 的许多团队中都造成过问题（参见 Rule #6)。 虽然用户没有见过的数据可以丢，但给剩下的数据乘上权重至关重要。意思是当你做30%抽样的时候，给一个10/3的权重。使用权重后，Rule#14中讨论的特征校准就能够起作用。

## Rule 31 - 在把线上和训练阶段的数据 join一个表时候要多加小心，表可能会变化

 比如说你把 doc id 和这些 id的 feature离线数据（比如评论数、点击量等）join 起来。对于训练和线上，这表的内容是不同的，那么你模型在训练和线上对于同一个 doc id 的预测也会不同。避免这种情况发生的最简单方式就是在线上把特征用日志保存下来（见 Rule #32）。如果表的更新比较慢，你也可以按照小时或者天级做快照。但是请记住，这也不能100%解决问题。

## Rule 32 - 在可能的时候在训练和线上尽可能复用代码

 批量处理和在线预测不同。在线预测，你必须在每个请求过来时候立即处理。（比如你必须对每个 query 做单独的查询），而在批处理时候，你可以合并任务（比如做一个 join）。服务阶段是在线预测，训练阶段是离线批处理。但是，有些情况下你可以在两者之间重用代码。比如，你可以创建一个系统专用的对象，这样在 join 或者是单独处理的时候，系统都能以一种非常易读的方式去存储，错误也能够及早被测试找到。然后，一旦你收集到了足够的信息，用一个通用的方法把这种人类易读的对象转化为模型可用的数据。**这样就排除了一个训练服务偏差的源头。**作为必然的结果，你最好别在训练和线上服务阶段用两种编程语言，这样会使得复用代码几乎不可能。

## Rule 33 - 如果你用1月5号之前的数据训练的系统，那么用1月6号和之后的数据去测试系统

 总得来说，测试数据的时间不要和训练数据的时间重叠，这样才能体现模型的预测能力。如果你用1月5号之前的数据训练的系统，那么用1月6号和之后的数据去测试系统。你可以预期模型在新数据的表现不会想训练时候那么好，但是也不应该太遭。也许会有天级效应，你也许不会去预测平均点击率或者转化率，但是曲线之下的空间（这里应该是指 AUC），应该会比较接近。

#### Rule 34 - In binary classification for filtering (such as spam detection or determining interesting e­mails), make small short­term sacrifices in performance for very clean data.

In a filtering task, examples which are marked as negative are not shown to the user. Suppose you have a filter that blocks 75% of the negative examples at serving. You might be tempted to
draw additional training data from the instances shown to users. For example, if a user marks an
email as spam that your filter let through, you might want to learn from that. But this approach introduces sampling bias. You can gather cleaner data if instead during
serving you label 1% of all traffic as “held out”, and send all held out examples to the user. Now your filter is blocking at least 74% of the negative examples. These held out examples can
become your training data. Note that if your filter is blocking 95% of the negative examples or more, this becomes less
viable. Even so, if you wish to measure serving performance, you can make an even tinier sample (say 0.1% or 0.001%). Ten thousand examples is enough to estimate performance quite
accurately.

#### Rule 35 - Beware of the inherent skew in ranking problems.

When you switch your ranking algorithm radically enough that different results show up, you have effectively changed the data that your algorithm is going to see in the future. This kind of skew will show up, and you should design your model around it. There are multiple different approaches. These approaches are all ways to favor data that your model has already seen.

1. Have higher regularization on features that cover more queries as opposed to those features that are on for only one query. This way, the model will favor features that are
specific to one or a few queries over features that generalize to all queries. This approach can help prevent very popular results from leaking into irrelevant queries. Note
that this is opposite the more conventional advice of having more regularization on feature columns with more unique values.
2. Only allow features to have positive weights. Thus, any good feature will be better than a feature that is “unknown”.
3. Don’t have document­only features. This is an extreme version of #1. For example, even if a given app is a popular download regardless of what the query was, you don’t want to
show it everywhere<sup>4</sup>. Not having document­only features keeps that simple.

<sup>4 - The reason you don’t want to show a specific popular app everywhere has to do with the importance of
making all the desired apps reachable. For instance, if someone searches for “bird watching app”, they
might download “angry birds”, but that certainly wasn’t their intent. Showing such an app might improve
download rate, but leave the user’s needs ultimately unsatisfied.</sup>

#### Rule 36 - Avoid feedback loops with positional features.

The position of content dramatically affects how likely the user is to interact with it. If you put an app in the first position it will be clicked more often, and you will be convinced it is more likely to be clicked. One way to deal with this is to add positional features, i.e. features about the position of the content in the page. You train your model with positional features, and it learns to weight, for example, the feature "1st­position" heavily. Your model thus gives less weight to other factors for examples with "1st­position=true". Then at serving you don't give any instances the positional feature, or you give them all the same default feature, because you are scoring candidates before you have decided the order in which to display them. Note that it is important to keep any positional features somewhat separate from the rest of the
model because of this asymmetry between training and testing. Having the model be the sum of a function of the positional features and a function of the rest of the features is ideal. For example, don’t cross the positional features with any document feature.

#### Rule 37 - Measure training/serving skew.

There are several things that can cause skew in the most general sense. Moreover, you can divide it into several parts:

1. The difference between the performance on the training data and the holdout data. In general, this will always exist, and it is not always bad.
2. The difference between the performance on the holdout data and the “next­day” data. Again, this will always exist. **You should tune your regularization to maximize the next­day performance.** However, large drops in performance between holdout and next­day data may indicate that some features are time-­sensitive and possibly degrading
model performance.
3. The difference between the performance on the “next­day” data and the live data. If you apply a model to an example in the training data and the same example at serving, it
should give you exactly the same result (see Rule **#5**). Thus, a discrepancy here probably indicates an engineering error.

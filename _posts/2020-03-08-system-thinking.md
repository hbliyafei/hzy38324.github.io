---
layout:     post                    # 使用的布局（不需要改）
title:    如何解决复杂问题             # 标题 
subtitle:   #副标题
date:       2020-03-08              # 时间
author:     ZY                      # 作者
header-img: img/banner/20190128/alyssa-graham-1322772-unsplash.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 思考
---

1.

有一个非常重要的项目给到你，这个项目涉及的影响面非常广，牵一发而动全身，你要如何设计？如何思考？

你发现有一些类似的 bug，经常在线上发现，你要怎么找到原因？

你发现总有那么些个流程，非常低效，你要怎么优化它们？

你发现上面三个问题，之前也有人尝试解决过，但是都没解决好，为什么？

这些问题有一个共同特征，那就是「复杂」。

**而系统思考的其中一个用途，就是解决复杂问题。**

2.

举一个关于内勤部门的例子，原故事用的股票交易来举例，最近看了《棒球大联盟》，那就以一只棒球队来解释吧。

所谓内勤，可以理解为就是在背后默默做事的那群人，对于一只棒球队，球员、教练是外勤，他们直接给球队的胜利做贡献，而内勤，则是数据分析师、球探、后勤部门等等，他们服务于球员和教练，给他们提供各种各样的服务，帮助他们赢得更多的胜利。

作为球队老板，你可以很明显感觉到有两件事情，会对球队带来影响：

- 内勤部门的服务质量：比如上次数据团队给了教练一份队里投手的分析报告，发现有一个叫吉利博的年轻球手，出场二十次，十八次三振（就是让对方出局的意思）对方击打手，让教练发现了一个队内隐藏的明星球员；
- 内勤部门的错误发生率：比如前几天球探花重金挖来了一个击打手，结果这家伙其实已经带病在身，过来后刚打了一场球，就宣布赛季报销了，对球探部门的这个失误，你怒不可遏。

而服务质量和错误发生率，都可以归结为内情部门的「处理能力」决定的，于是你画了下面一张图：

![](/img/post/2020-03-08-system-thinking/WechatIMG88.jpeg)  

其中 S 可以理解为正比，O 则可以理解为反比。处理能力的上升，会带来服务质量的上升，以及错误发生率的下降，这都很显而易见。

虽然显而易见，但是这张图清晰的告诉你，要想提供内勤部门对球队的帮助，关键就是提高内勤部门的「处理能力」。

3.

这时候你想到之前有内勤部门的员工在抱怨，“妈的，天天加班，一天才睡 4 个小时”，于是你给这张图补了两条线：

![](/img/post/2020-03-08-system-thinking/WechatIMG89.jpeg)  

这里的交易数量和种类，对应的是原故事的股票交易，这里我们就理解为比赛的数量吧（通常一只球队越厉害，打的比赛也会越多，需要内勤部门的服务也越多）。

你继续思考，错误发生了会不会对这个系统有什么影响？哦，对了，员工发生了错误，上级以及其他同事就需要加入错误排查、复盘等等工作中，无形中加大了管理成本，继而又增加了工作负荷：

![](/img/post/2020-03-08-system-thinking/WechatIMG92.jpeg)  

没想到吧，工作负荷-错误发生率，竟然形成了一个「恶性循环」。

你继续思考，有什么，是可以提高处理能力的吗？嗯，那自然是内勤部门下那些能干的员工了，而能干的员工，一是来自招聘的员工数量，二是来自培训；甚至你还发现一个高效的内部 IT 系统，是可以提高工作效率，从而降低工作负荷的，虽然引入一个 IT 系统不能马上就起作用（有时滞）：

![](/img/post/2020-03-08-system-thinking/WechatIMG91.jpeg)  

4.

这图越来越完善了，但还缺少一个很重要的东西，作为老板，最看重的，除了绩效，自然还有成本，用最低的成本干最大的事，这不是资本金最擅长的吗？

于是你把图中已有的元素跟成本的关系都补了上去：

![](/img/post/2020-03-08-system-thinking/WechatIMG88.jpeg)  

这里有个连线特别重要，那就是「错误发生频率」和「成本」的连线，你如果成本投入过低，就会降低内勤部门的处理能力，处理能力的下降最后又会提高错误发生频率，而错误发生频率的提高，反而会提高你的成本。

**这简直太诡异了，你成本投入的降低，反而会提高你的成本。**

虽然最后你还是陷入了那个老问题：到底要投入多少成本？但是你却对内勤部门，这个系统非常了了解，下一步你就是要研究下，到底把成本更多的花在哪方面更有效：是招聘更多的员工，还是加大培训的投入，还是搭建一套高效的 IT 系统？

**真正深刻而且不同寻常的洞察力，来自观察系统如何塑造自己的行为方式。**

这句话放在这里，再合适不过了。

5.

聊一下我对系统思考的理解。

系统思考，这里的系统，我理解有两层含义。

**一层是意识层面的**，就是你在解决一个复杂问题的时候，需要有意识的站在系统、站在全局的角度去思考问题，不能只见树木，不见森林；

**另外一层，则是行动层面**，当你有了系统的意识后，就要去通过各种各样的方式，谷歌也好、看书也好、请教别人也好，去构建你对系统的认知，画出你的系统图。

6.

画出系统图，有几个好处：

**一、通过画图的方式，不断的问自己“为什么”、“然后呢”、“还有什么吗”，帮助你提高思考的深度。**

我有一种感觉，就是如果不把想法画出来，或者写出来，而只在脑子里思考，思考的层次通常只有两层。比如思考为什么员工错误率会提升，如果只在脑子里想，那我会瞬间想到工作压力大，然后，也许就没然后了。而把想法画出来，则可以发现工作负荷和错误率的恶性循环，甚至发现工作负荷和成本的关系。

其实这就是一个把负担从大脑转移到别处，让大脑可以有更多的能量去思考的方法。

**二、通过分析系统图，找出关键杠杆点。**

当你把图画出来的时候，你可以通过观察这张图，来找出哪些节点，是可能可以起到四两拨千斤的作用的杠杆点，从而尝试对这些杠杆点施力，来影响系统。

**还是那句话，真正深刻而且不同寻常的洞察力，来自观察系统如何塑造自己的行为方式，因为在观察到系统如何塑造自己后，你就可以去影响系统，从而让系统塑造别人的行为方式。**

**三、用画系统图的方式，来帮助团队达成共识，提升交流的效率。**

就像 *丹尼斯·舍伍德* 在《系统思考》里说的：

> 当不同人绘制的系统循环图相互比较时，这是一种礼貌的向别人描述自己世界观的稳妥方式。
>
> 如果一幅图能够让团队中每个人看到它都会说，“对，我也是这么看的”，大家就会在解决问题、制定政策和一致行动方面处于非常有利的位置。

有些时候，你会发现你在会上巴拉巴拉说了一大堆，会上大家都觉得听懂了，但是真正开干的时候，你发现其实大家的理解跟你的都不一致。而把你对系统的理解、你的想法，用画图的方式展示出来，就是一种很好的帮助团队达成共识的方法。

这也是为什么一个复杂的技术方案，必须要有展示各个模块交互逻辑的流程图的原因。

7.

关于系统思考，有三本书肯定是要推荐的，这也是我最近在看的三本书：

- 《系统之美》：一本系统思考的入门书籍
- 《第五项修炼》：把系统思考应用到管理领域，用系统思考来提升团队核心学习能力，提升团队解决复杂问题的能力
- 《系统思考》：比起前面两本，这本书更有实操性，作者分享了系统思考在很多场景下的实际使用，给出了很多系统思考和画系统图的建议，也提出了一些和《第五项修炼》不同的观点

8.

最后推荐一部充满系统思考美感的剧 —— 《棒球大联盟》，剧中主人公是一个棒球球队的团长，在这部剧里你可以看到他是如何用各种系统思考的方式，在资金条件及其匮乏的情况下，把一只弱队一步步打造变强的。

**顺便说一句，神剧都是系统思考的，通过看这些神剧，你可以了解到行业底层的逻辑。**比如看这部《棒球大联盟》，就让我了解到了一只球队背后是怎么运作的，它和赞助它的公司之间是什么关系等等，也可以看出为了拍这部剧，编剧需要了解很多行业相关的知识，也许还要聘请运营棒球球队的专家作为顾问。

而一些狗血剧，往往没有什么思考，反正最后都会变成爱情剧。






















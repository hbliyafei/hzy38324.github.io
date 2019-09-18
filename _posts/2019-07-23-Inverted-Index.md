---
layout:     post                    # 使用的布局（不需要改）
title:    聊聊 Elasticsearch 的倒排索引    # 标题 
subtitle:   #副标题
date:       2019-07-23              # 时间
author:     ZY                      # 作者
header-img: img/banner/20190128/miguel-angel-hernandez-1322895-unsplash.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 倒排索引
    - 算法
    - ES
    - Lucene
---



# 为什么需要倒排索引  

倒排索引，也是索引。

索引，初衷都是为了快速检索到你要的数据。  

**每种数据库都有自己要解决的问题（或者说擅长的领域），对应的就有自己的数据结构，而不同的使用场景和数据结构，需要用不同的索引，才能起到最大化加快查询的目的。**  

对 Mysql 来说，是 B+ 树，对 Elasticsearch/Lucene 来说，是倒排索引。  

> Elasticsearch 是建立在全文搜索引擎库 Lucene 基础上的搜索引擎，它隐藏了 Lucene 的复杂性，取而代之的提供一套简单一致的 RESTful API，不过掩盖不了它底层也是 Lucene 的事实。
>
> Elasticsearch 的倒排索引，其实就是 Lucene 的倒排索引。



# 为什么叫倒排索引  

在没有搜索引擎时，我们是直接输入一个网址，然后获取网站内容，这时我们的行为是：  

document -> to -> words  

通过文章，获取里面的单词，此谓「正向索引」，forward index.  

后来，我们希望能够输入一个单词，找到含有这个单词，或者和这个单词有关系的文章：

word -> to -> documents

于是我们把这种索引，成为inverted index，直译过来，应该叫「反向索引」，国内翻译成「倒排索引」，有点委婉了。

现在思考一下，如果让你来设计这个可以通过单词，反向找到文章的索引，你会怎么实现？   

> 关于 Elasticsearch 这类「搜索引擎」要解决的问题、它和传统关系型数据库的区别等等，可以看我之前写的文章：为什么需要 Elasticsearch（文末有链接）



# 倒排索引的内部结构

首先，在数据生成的时候，比如爬虫爬到一篇文章，这时我们需要对这篇文章进行分析，将文本拆解成一个个单词。

这个过程很复杂，比如“生存还是死亡”，你要如何让分词器自动将它分解为“生存”、“还是”、“死亡”三个词语，然后把“还是”这个无意义的词语干掉。这里不展开，感兴趣的同学可以查看文末关于「分析器」的链接。

接着，把这两个词语以及它对应的文档id存下来：  

word documentId  

生存  1

死亡  1

接着爬虫继续爬，又爬到一个含有“生存”的文档，于是索引变成：

word documentId  

生存  1,2

死亡  1

下次搜索“生存”，就会返回文档ID是 1、2两份文档。

然而上面这套索引的实现，给小孩子当玩具玩还行，要上生产环境，那还远着。

想想看，这个世界上那么多单词，中文、英文、日文、韩文 … 你每次搜索一个单词，我都要全局遍历一遍，很明显不行。  

于是有了排序，我们需要对单词进行排序，像 B+ 树一样，可以在页里实现二分查找。

光排序还不行，你单词都放在磁盘呢，磁盘 IO 慢的不得了，所以 Mysql 特意把索引缓存到了内存。

你说好，我也学 Mysql 的，放内存，3，2，1，放，哐当，内存爆了。

哪本字典，会把所有单词都贴在目录里的？

所以，上图：  

![](/img/post/2019-07-23-Inverted-Index/1.png)  

Lucene 的倒排索，增加了最左边的一层「字典树」term index，它不存储所有的单词，只存储单词前缀，通过字典树找到单词所在的块，也就是单词的大概位置，再在块里二分查找，找到对应的单词，再找到单词对应的文档列表。

当然，内存寸土寸金，能省则省，所以 Lucene 还用了 FST（Finite State Transducers）对它进一步压缩。

FST 是什么？这里就不展开了，这次重点想聊的，是最右边的 Posting List 的，别看它只是存一个文档 ID 数组，但是它在设计时，遇到的问题可不少。



# Frame Of Reference

原生的 Posting List 有两个痛点：

- **如何压缩以节省磁盘空间**
- **如何快速求交并集（intersections and unions）**

先来聊聊压缩。

我们来简化下 Lucene 要面对的问题，假设有这样一个数组：

[73, 300, 302, 332, 343, 372]  

如何把它进行尽可能的压缩？

Lucene 里，数据是按 Segment 存储的，每个 Segment 最多存 65536 个文档 ID， 所以文档 ID 的范围，从 0 到 2^32-1，所以如果不进行任何处理，那么每个元素都会占用 2 bytes ，对应上面的数组，就是 6 * 2 = 12 bytes.  

怎么压缩呢？

**压缩，就是尽可能降低每个数据占用的空间，同时又能让信息不失真，能够还原回来。**

**Step 1：Delta-encode —— 增量编码**

我们只记录元素与元素之间的增量，于是数组变成了：

[73, 227, 2, 30, 11, 29]

**Step 2：Split into blocks —— 分割成块**

Lucene里每个块是 256 个文档 ID，这样可以保证每个块，增量编码后，每个元素都不会超过 256（1 byte）.

为了方便演示，我们假设每个块是 3 个文档 ID：

[73, 227, 2], [30, 11, 29]

**Step 3：Bit packing —— 按需分配空间**

对于第一个块，[73, 227, 2]，最大元素是227，需要 8 bits，好，那我给你这个块的每个元素，都分配 8 bits的空间。

但是对于第二个块，[30, 11, 29]，最大的元素才30，只需要 5 bits，那我就给你每个元素，只分配 5 bits 的空间，足矣。

这一步，可以说是把吝啬发挥到极致，精打细算，按需分配。

以上三个步骤，共同组成了一项编码技术，Frame Of Reference（FOR）：

![](/img/post/2019-07-23-Inverted-Index/2.png)  



# Roaring bitmaps

接着来聊聊 Posting List 的第二个痛点 —— 如何快速求交并集（intersections and unions）。

在 Lucene 中查询，通常不只有一个查询条件，比如我们想搜索：

- 含有“生存”相关词语的文档
- 文档发布时间在最近一个月
- 文档发布者是平台的特约作者

这样就需要根据三个字段，去三棵倒排索引里去查，当然，磁盘里的数据，上一节提到过，用了 FOR 进行压缩，所以我们要把数据进行反向处理，即解压，才能还原成原始的文档 ID，然后把这三个文档 ID 数组在内存中做一个交集。

> 即使没有多条件查询， Lucene 也需要频繁求并集，因为 Lucene 是分片存储的。

同样，我们把 Lucene 遇到的问题，简化成一道算法题。

假设有下面三个数组：

[64, 300, 303, 343]  

[73, 300, 302, 303, 343, 372]  

[303, 311, 333, 343]  

求它们的交集。

**Option 1: Integer 数组**

直接用原始的文档 ID ，可能你会说，那就逐个数组遍历一遍吧，遍历完就知道交集是什么了。

其实对于有序的数组，用跳表（skip table）可以更高效，这里就不展开了，因为不管是从性能，还是空间上考虑，Integer 数组都不靠谱，假设有100M 个文档 ID，每个文档 ID 占 2 bytes，那已经是 200 MB，而这些数据是要放到内存中进行处理的，把这么大量的数据，从磁盘解压后丢到内存，内存肯定撑不住。

**Option 2: Bitmap**

假设有这样一个数组：

[3,6,7,10]

那么我们可以这样来表示：

[0,0,1,0,0,1,1,0,0,1]

看出来了么，对，**我们用 0 表示角标对应的数字不存在，用 1 表示存在。**

这样带来了两个好处：

- 节省空间：既然我们只需要0和1，那每个文档 ID 就只需要 1 bit，还是假设有 100M 个文档，那只需要 100M bits = 100M * 1/8 bytes = 12.5 MB，比之前用 Integer 数组 的 200 MB，优秀太多
- 运算更快：0 和 1，天然就适合进行位运算，求交集，「与」一下，求并集，「或」一下，一切都回归到计算机的起点

**Option 3: Roaring Bitmaps**

细心的你可能发现了，bitmap 有个硬伤，就是不管你有多少个文档，你占用的空间都是一样的，之前说过，Lucene  Posting List 的每个 Segement 最多放 65536 个文档ID，举一个极端的例子，有一个数组，里面只有两个文档 ID：

[0, 65535]

用 Bitmap，要怎么表示？

[1,0,0,0,….(超级多个0),…,0,0,1]

你需要 65536 个 bit，也就是 65536/8 = 8192 bytes，而用 Integer 数组，你只需要 2 * 2 bytes = 4 bytes

呵呵，死板的 bitmap。可见在文档数量不多的时候，使用 Integer 数组更加节省内存。

我们来算一下临界值，很简单，无论文档数量多少，bitmap都需要 8192 bytes，而 Integer 数组则和文档数量成线性相关，每个文档 ID 占 2 bytes，所以：

8192 / 2 = 4096

当文档数量少于 4096 时，用 Integer 数组，否则，用 bitmap.  

![](/img/post/2019-07-23-Inverted-Index/3.png)  

> 这里补充一下 Roaring bitmaps 和 之前讲的 Frame Of Reference 的关系。
>
> Frame Of Reference 是压缩数据，减少磁盘占用空间，所以当我们从磁盘取数据时，也需要一个反向的过程，即解压，解压后才有我们上面看到的这样子的文档ID数组：[73, 300, 302, 303, 343, 372]  ，接着我们需要对数据进行处理，求交集或者并集，这时候数据是需要放到内存进行处理的，我们有三个这样的数组，这些数组可能很大，而内存空间比磁盘还宝贵，于是需要更强有力的压缩算法，同时还要有利于快速的求交并集，于是有了Roaring Bitmaps 算法。
>
> 另外，Lucene 还会把从磁盘取出来的数据，通过 Roaring bitmaps 处理后，缓存到内存中，Lucene 称之为 filter cache.  



# 升华与总结

文章的最后，如果来一段话总结（zhuang）升华（bi）一下，这篇文章就会得高分。

有什么总结，可以拔高这篇文章的高度呢？

**首先，你会发现，很多业务上、技术上要解决的问题，最后都可以抽象为一道算法题，复杂问题简单化。**

呃，这个“华”，升的还不够。

另一个具有高度的“华”，其实在开头已经讲出来了：

**每种数据库都有自己要解决的问题（或者说擅长的领域），对应的就有自己的数据结构，而不同的使用场景和数据结构，需要用不同的索引，才能起到最大化加快查询的目的。**

这篇文章讲的虽是 Lucene 如何实现倒排索引，如何精打细算每一块内存、磁盘空间、如何用诡谲的位运算加快处理速度，但往高处思考，再类比一下 Mysql，你就会发现，虽然都是索引，但是实现起来，截然不同。

这个往细讲，又是一篇文章：**如此不同，如此成功 —— B+ 树索引 vs 倒排索引**

标题都想好了，就看各位爷了，点赞超 50 就写 …  

可能没机会写了，那就 …  



# 留个作业吧

知识要融合起来看才有意思。

来，放大招了，两个问题：

- Lucene 为什么不用 b+ 树来搜索数据？

- Mysql 为什么不用 倒排索引来检索数据？

附上两张图：

![Mysql 的 B+树索引](/img/post/2018-07-21-Mysql-Index/btree-index-02.png)

![Lucene 的倒排索引](/img/post/2019-07-23-Inverted-Index/1.png)  



# 参考

- [Frame of Reference and Roaring Bitmaps](https://www.elastic.co/blog/frame-of-reference-and-roaring-bitmaps)

- [Stackoverflow: What's the difference between an inverted index and a plain old index?](https://stackoverflow.com/questions/7727686/whats-the-difference-between-an-inverted-index-and-a-plain-old-index)

- [Elasticsearch权威指南：分析与分析器](https://www.elastic.co/guide/cn/elasticsearch/guide/current/analysis-intro.html)

- [Elasticsearch 技术分析（七）： Elasticsearch 的性能优化](https://www.cnblogs.com/jajian/p/10465519.html)

- [为什么需要 Elasticsearch](https://zhuanlan.zhihu.com/p/73585202)








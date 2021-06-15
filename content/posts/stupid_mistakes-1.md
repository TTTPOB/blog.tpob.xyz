---
title: "有一些人他会犯一些很愚蠢的错误-1"
date: 2021-05-21T19:37:00+08:00
draft: false
---

唉有的人吧屁事不干文章也不想看，就想水。但是呢他又很菜没什么可以水的，而且每天还犯一堆错误；那既然如此为什么不开一个贴子记下来呢（

## 2021.05.21 这人bowtie2 index格式都可以记错


嗯是的我知道这里不应该有`.fa`，但我居然排除了一下午。

以及我强烈谴责写脚本碰到异常不退出的行为，我翻日志都不知道哪里错的（

为了其它找不到问题在哪里的人说不定可以搜到这个文章我把可能会搜索的东西放在这里
`(FILE LOCATION) does not exist or is not a Bowtie 2 index`

![脚本里的路径](https://cdn.jsdelivr.net/gh/TTTPOB/imageHost/20210521194119.png)
![这个目录里面的东西](https://cdn.jsdelivr.net/gh/TTTPOB/imageHost/20210521194231.png)

## 2021.06.01 这人之前说ebi/ncbi的测序数据没给checksum不能验证完整性

万一有人记得我写过一个[特小脚本](https://blog.tpob.xyz/2021/03/24/%E6%9B%B4%E5%BF%AB%E4%B8%8B%E8%BD%BDsra%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%AD%E7%9A%84fastq.gz%E6%96%87%E4%BB%B6/)来从ebi直接下`fastq.gz`数据以免被`sratoolkit`慢死。

但是这样不带checksum下下来文件损坏也不知道，心里很慌，所以我到处喷ncbi/ebi的ftp站点不给文件放checksum，今天把一个巨大的`fastq.gz`文件扔进pipeline里面之后才知道是损坏的，非常浪费时间。

但实际上`gzip`文件内部是自带有checksum的，虽然是比较弱的`CRC32`但是也可以基本保证正确性了，使用也非常简单，只需要`gzip -v -t theFile.gz`就可以了。结果大概长这样：

![结果的样子](https://cdn.jsdelivr.net/gh/TTTPOB/imageHost/20210601203859.png)

当然啦，嫌慢的话总可以用神奇的`parallel`：`ls *gz|parallel -j4 gzip -v -t {}`(当心线程太多把服务器io卡爆)。

搜索关键词：gzip checksum ncbi ebi sra fastq 数据完整性校验

## 2021.06.15 他被jupyter IRKernel不该输出的图片折磨了一天然后发现很简单可以关掉

有的时候你被迫使用`base r`的绘图工具进行画图，比如很简单的画一个density，但如果你在对一个循环/`lapply`做这件事，那你会被输出的图片占满整个屏幕，太长了滚动起来也不方便；即使你使用`grid.echo();p1<-grid.grab()%>%as.ggplot()`来保存了这张图片（转换成ggplot对象），但画图的时候还是不可避免地会输出在cell的output里，即使你使用了[invisible](https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/invisible)（很好理解，它只是不让对象显示，但`plot`也不是这样显示出来的）。但我刚刚突然想到我直接

```R
library(magrittr)
library(grid)
library(gridGraphics)
library(ggplotify)

## 我直接在这里把结果输出到null设备不就好了吗
pdf(file="/dev/null",w=1,h=1)
plot(1,1)
grid.echo()
p1<-grid.grab()%>%as.ggplot
dev.off()
```
我直接把结果输出到`/dev/null`不就好了吗！
（突然觉得我是不是应该用`jupyter`写blog，反正也是markdown）
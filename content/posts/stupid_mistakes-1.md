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
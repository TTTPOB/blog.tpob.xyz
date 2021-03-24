---
title: "更快下载sra数据库中的fastq.gz文件"
date: 2021-03-24T14:36:15+08:00
draft: true
---

啊狗屎ncbi的sra数据每次都要`prefetch`下下来再`fastq-dump`/`fasterq-dump`，真的是气死我了，prefetch还是单线程，在众所周知的网络环境下面的速度一言难尽...

好在ebi数据库不仅与ncbi同步更新而且还直接给了`fastq.gz`的下载链接，甚至有给你json api来根据`accession number`获取，所以我们可以有如下的操作。

<!-- more -->

打开你需要的GSE页面进入`SRA RUN Selector`，下下来之后里面应该长这样
```
cat example.list

SRR000999
SRR000998
SRR000997
```
然后我们使用[临时摸出来的这个脚本](https://gist.github.com/TTTPOB/9c52a1ebd10ee7383bda245663e252a0)，和parallel，
```bash
cat example.list|parallel SRR2fqgz.py | parallel aria2c -x16 -s16 {}
```
它应该就可以下好了


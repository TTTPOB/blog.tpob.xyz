---
title: "更快获得srr accession"
date: 2021-10-14T21:55:34+08:00
draft: true
---

上次我们在摸鱼的时候写了[一个jio本](https://blog.tpob.xyz/2021/03/24/%E6%9B%B4%E5%BF%AB%E4%B8%8B%E8%BD%BDsra%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%AD%E7%9A%84fastq.gz%E6%96%87%E4%BB%B6/)来帮助我们从srr直接下载fastq.gz，但问题来了怎么快速拿到srr呢？有的时候(很多时候)NCBI自己的SRA Run Selector并不好用，并且我可能只是想下一个GSM样本对应的所有sra文件而已，这个时候在页面上点来点去就很麻烦，下下来之后还要自己对着网页重命名，眼睛都要瞎了，所以我今天又在摸鱼的时候写了另一个jio本，[gist链接](https://gist.github.com/TTTPOB/983ec24cf42a01b7802098ee6f117494)。

上次我是想着为了尽量减少依赖就全用的标准库，requests都没用，直接用裸的urllib3写的，非常费劲，这次我决定节约一下我的大脑直接用`httpx`，`BeautifoulSoup4`，`click`，《非常现代》。

大概的用法我相信能看到这个文章的人可以自己明白，比如你有一个gsm号码`GSM1111111`，你就
```bash
#改名字，加PATH应该没有不会的吧（
get_srr gsm2srr GSM1111111 > GSM1111111.srr
srr2fqgz GSM1111111.srr > GSM1111111.srr_linklist
#aria2c下下来，wget啥的都行
aria2c -i GSM1111111.srr_linklist
#记得校验
gzip -vt *gz

#或许你还想把不同run的数据拼到一起
yes _1.fastq.gz|head -n `wc -l GSM1111111.srr`
tr 1 2 _1 > _2
paste GSM1111111.srr _1 -d "" | xargs cat {} > {samplename}_R1.fastq.gz 
paste GSM1111111.srr _2 -d "" | xargs cat {} > {samplename}_R2.fastq.gz 
```
这个命令就可以获得GSM1111111对应的所有srr，然后你就可以用`cat GSM1111111.srr`来获得所有srr，然后用`srr2fqgz`拿到地址，然后就`aria2c`一把梭，记得校验一下完整性。

然后我在这里安利一下`click`，[官网在这](https://click.palletsprojects.com/)，相当好用，完全不需要大脑，比`argparse`好用一万倍。

好了水完了。
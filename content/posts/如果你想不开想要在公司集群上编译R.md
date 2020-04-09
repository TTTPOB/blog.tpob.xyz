---
title: 如果你想不开想要在公司集群上编译R
date: 2018-12-03 23:25:19
tags:
---
# 如果你想不开想要在公司集群上编译R
我前两天做了一个让我非常痛苦的决定。  
我们公司的集群，IO极差，删anaconda文件夹可能要删三天。  
我们公司的集群，为了安全考虑，一般人没有root权限。
我们公司的集群，不光没有root权限，软件和工具链也都完全不全。  
所以我选择使用Anaconda当包管理器，但是一旦anaconda内部软件更新，外部依赖这些动态链接库的软件就会集体GG。比如htop、tmux(其实tmux也是我用anaconda装的)和自己装的ssh(为什么要自己装emmm，有缘再讲)。而且我平时用的最多的是R，但是R这个东西编译起来十分蛋疼所以我也用anaconda装了。  
但是。  
但是，anaconda里面的R版本更新特别慢，包的更新也特别慢，而且anaconda处理依赖的时候有我无法看出规律的诡异升级和降级过程，导致经常装一个新包的时候告诉你R要降级。  

## 那就自己编译一个R！
<!--more-->
好的，既然我想自己编译一个R，目的是摆脱anaconda对我的束缚。那我应该build everything from source对不对？好的，从这里开始就掉进坑里了。
公司服务器上预装的gcc是4.4.7版本的，不能满足编译R的需要，我们需要更新gcc。记住，没有包管理器。

## GCC
编译gcc的文章网上有很多，这里我们最好选择7.x（因为我用8.x被坑了，8.x有些改动是不向下兼容的，很多包会编译失败）。GCC有三个依赖，同样的，系统上的版本并不能够满足版本要求。三个依赖分别是mpc, mpfr, gmp。如果你有管理员权限请直接用包管理器。。。

## 被IO折磨
我司集群io极差，你不能想象的那种。你按下cd自动补全比手敲还慢。网速也只有1m，总之emmm。好了我们继续。  
编译R需要readlines依赖（可选，但我建议你加上，不然用不了方向键）,我是参考这个人的[文章](https://davetang.org/muse/2018/02/06/compiling-r-gnu-readline/)进行编译的，他说的很清楚，把我从一个大坑里拉了出来。编译时的`LDFLAGS`和`CPPFLAGS`如果不写，即使你在别的什么地方指定了readline/ssl/curl，R也是不会认的。（`LD_LIBRARY_PATH`也要写好）
经过无数天的素质三连(`../configure blahblah`然后`make -j50`然后`make install`)我往`.zshrc`里加入了两个alias。  
```bash
alias pszll="mkdir build_dir && cd build_dir"
alias szll="make -j50 && make install"
```
`configure`就没写进去了，毕竟那么多参数要改。  
好的，由于集群的io问题，我等待了半天(实指)后终于装好了。  
加入环境变量，设好`R_LIBS_USER`作为自己以后装包的目录(强烈推荐，这样你在自己装的包出问题之后可以毫无负担地删掉这个目录)
看上去没什么问题，我表示可以启动R。

## 坑爹的IRkernel
由于我最终还是要在Jupyter lab里面使用R的，于是手动安装一个IRkernel。一切正常，直到我发现在jupyter里面执行任何代码都会导致kernel died（  
我发现它报的错误是跟x有关，遂查到[这个issue](https://github.com/IRkernel/IRkernel/issues/388)，可是我明明是加了`--with-x=no`的，实在不知道为什么（  
由于时间有限加上我的工作并不是运维。。。所以我最终屈服了，使用了anaconda来安装R（好水啊这个文章图都没有）。
当然我也有隔离我的R的！新开了一个环境里面只有R和R包的依赖，然后在要用到python的时候使用别的环境（你能想象我司辣鸡IO让我浪费了多少生命吗）。
---
title: "Glow"
date: 2021-04-28T20:18:40+08:00
draft: false
---

## 命令行markdown渲染工具glow

唉这个实在是太水了但是由于我觉得可以借机宣扬一下在服务器的每个文件夹都放一个`README.md`所以我还是写一个（

总之就是今天整理师姐留下来的一个文件夹发现她的数据位置啊，来源啊，要用到的工具啊，什么都没写，这样就很麻烦很不好用（嗯虽然其实我经常也不写）。

但是呢如果你直接写一个markdown又会很难看比如长成这样 ![plain text markdown](https://cdn.jsdelivr.net/gh/TTTPOB/imageHost/20210428202738.png)，这样怎么说呢，丑不说，也不便于突出重点。

### 立刻使用 [glow](https://github.com/charmbracelet/glow)
然后我一搜`cli markdown render`就找到[glow](https://github.com/charmbracelet/glow)，去release下好之后用一下大概就是长这样 ![prettier markdown](https://cdn.jsdelivr.net/gh/TTTPOB/imageHost/20210428203118.png)

这样看起来还是舒服多了，自己写起来动力也稍微足一点毕竟稍微好看那么一些。

这工具还有tui模式，如果你的文档实在是太长了想必是用得上的。（直接`glow xx.md | less`是不行的，因为less显然没有办法显示这样的样式）

好了我水完了，希望大家养成整理文件和说明的好习惯。
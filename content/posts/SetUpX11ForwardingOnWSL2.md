---
title: "在WSL2上修好到服务器的X11转发"
date: 2020-12-11T13:41:33+08:00
draft: false
---

大约一年以前我从`WSL1`“升级”到`WSL2`，首先遇到的问题是以前常用的在Windows上跑一个x服务器然后用x11监听到wsl里来用图形软件的做法似乎不能用了：

`Error: Can't open display: localhost:10`

如果有人需要解释为什么是`localhost:10`而不是`:10`那你可以先去查一查\(

解决办法比较简单，是把`localhost:10`换成`$WINDOWS_IP:10`当然这个10可以是你想要的任何端口号。（我保持这个状态到了今天）

~~心情好的话我改天更新一下把涉及到的所有奇怪操作都写一下免得你们还要去别处查~~

但当你这样弄完之后你会发现你SSH到实验室服务器的时候会`Warning: untrusted X11 forwarding setup failed: xauth key data not generated`，并且确实无法使用服务器的X11软件，即使`ssh -Y`也不行。

那我只能`ssh -vvv`看看出了什么事，发现卡在这里：

`debug2: client_x11_get_proto: xauth command: /usr/sbin/xauth -f /tmp/ssh-V0tly0ULHMm2/xauthfile generate 172.26.112.1:10 MIT-MAGIC-COOKIE-1 untrusted timeout 1260 2>/dev/null`

其中`172.26.112.1:10`是我刚刚设置的`$WINDOWS_IP:10`，所以似乎...`xauth`没有办法在target不在本机的时候进行认证？看起来好像是这样，但我们又不能设置到`WSL2`的`localhost`这样windows上的x服务器认不到端口...

但是

我们

可以用

端口转发

去nmdX11转发

（

我是这么干的

在服务器上的`.whatevershrc`:

`export DISPLAY=localhost:10`

在本地用带端口转发的ssh

`ssh $targetserver -R 6010:localhost:6010`

另开一个本地终端

`socat tcp-l:6010,fork,reuseaddr tcp:$WINDOWSIP:6010 &`

然后记得在windows打开你的xserver并设置好监听到10，当然如果你开在别的端口就监听别的，上面也都改成你自己设置的。

好了我们在服务器试试`xeyes`。

今天没带鼠标懒得插图总之我成功了嗯。

啊你问我为什么一年以前的事情现在发，，，其实是我忍了一年今天才知道为啥出了问题（

哦中英文互联网世界好像都没有人有这个问题，估计他们没有我这么扭曲的需求吧。

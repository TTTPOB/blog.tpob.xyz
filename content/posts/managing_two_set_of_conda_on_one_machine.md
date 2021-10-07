---
title: "一台机器上有好几套conda怎么办"
date: 2021-10-07T18:43:09+08:00
draft: true
---

其实这一篇也很水，可是又好几个月不更新了我能怎么办呢

## 背景

故事是这样的：

我们实验室用在我来之前就用[全局安装](https://docs.conda.io/projects/conda/en/latest/user-guide/configuration/admin-multi-user-install.html)的办法装了一套公用的conda，可是我当时没有管理员权限，不好换mamba或者进行类似的操作，所以用起来很难受，所以我就往自己的`$HOME`里装了自己的`miniconda`，但是给其它同学debug的时候经常要回到系统自带的conda环境里面，我就很烦，前两天就往自己的`.zshrc`里面写了个：

```zsh
function use_system_conda(){
  __conda_setup="$('/lab_public/shared/applications/conda/miniconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
  if [ $? -eq 0 ]; then
      eval "$__conda_setup"
  else
      if [ -f "/lab_public/shared/applications/conda/miniconda3/etc/profile.d/conda.sh" ]; then
          . "/lab_public/shared/applications/conda/miniconda3/etc/profile.d/conda.sh"
      else
          export PATH="/lab_public/shared/applications/conda/miniconda3/bin:$PATH"
      fi
  fi
  unset __conda_setup
  export WHICH_CONDA__="system conda"
}

function use_own_conda(){
  __conda_setup="$('/home/my_user_name/anaconda/bin/conda' 'shell.zsh' 'hook' 2> /dev/null)"
  if [ $? -eq 0 ]; then
      eval "$__conda_setup"
  else
      if [ -f "/home/my_user_name/anaconda/etc/profile.d/conda.sh" ]; then
          . "/home/my_user_name/anaconda/etc/profile.d/conda.sh"
      else
          export PATH="/home/my_user_name/anaconda/bin:$PATH"
      fi
  fi
  unset __conda_setup
  export WHICH_CONDA__="own conda"
}

use_own_conda

function get_which_conda(){
    echo $WHICH_CONDA__
}

PROMPT="%F{blue}(\$(get_which_conda))%f$PROMPT"
```

这样就方便多了，我想用自己的conda就`use_own_conda`(默认)想用系统的conda就`use_system_conda`，最后面一行还往prompt里面加了个提示提醒我用的是哪一个(是的我今天用错了才加的)，如果有使用`bash`的同学想抄作业记得改一下设置prompt的操作，这个是`zsh` only的，它设置颜色什么的要方便一些。

效果嘛

![效果图](https://cdn.jsdelivr.net/gh/TTTPOB/imageHost/20211007191250.png)
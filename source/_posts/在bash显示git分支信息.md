---
title: 在bash显示git分支信息
tags:
  - bash
  - git
  - linux
url: 629.html
id: 629
comments: false
categories:
  - 前端
date: 2018-11-17 16:43:54
---

**不推荐使用,请直接使用zsh**
==================

**linux**

    vim ~/.bashrc
    

    function git_branch {
      branch="`git branch 2>/dev/null | grep "^\*" | sed -e "s/^\*\ //"`"
      if [ "${branch}" != "" ];then
          if [ "${branch}" = "(no branch)" ];then
              branch="(`git rev-parse --short HEAD`...)"
          fi
          echo " ($branch)"
      fi
    }
    export PS1='\u@\h \[\033[01;36m\]\W\[\033[01;32m\]$(git_branch)\[\033[00m\] \$ '
    

    source ~/.bashrc
    

**mac** 1\. 同上 2. mac启动后加载的是.bash_profile

    echo "[ -r ~/.bashrc ] && source ~/.bashrc" >> .bash_profile
    

[或者直接使用zsh](https://ohmyz.sh/)
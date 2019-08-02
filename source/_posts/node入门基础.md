---
title: node入门基础
date: 2019-07-26 15:48:00
tags:
---
#### 安装

**开发环境**

[安装nvm](https://github.com/nvm-sh/nvm)

```shell
$ wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
```

添加以下代码到~/.bashrc, ~/.profile, or ~/.zshrc

```shell
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

nvm命令
```shell
$ nvm install 6.14.4  # 安装or 10.10.0, 8.9.1, etc
$ nvm ls 查看
```

安装cnpm
```shell
$ npm --registry=https://registry.npm.taobao.org install cnpm -g
```

#### 待续
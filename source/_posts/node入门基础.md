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

```shell
# 添加以下代码到~/.bashrc, ~/.profile, or ~/.zshrc
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

nvm命令
```shell
$ nvm install 6.14.4  # 安装or 10.10.0, 8.9.1, etc
$ nvm ls  # 查看
default -> 10.16.0 (-> v10.16.0)
node -> stable (-> v10.16.0) (default)
stable -> 10.16 (-> v10.16.0) (default)
iojs -> N/A (default)
lts/* -> lts/dubnium (-> v10.16.0)
lts/argon -> v4.9.1 (-> N/A)
lts/boron -> v6.17.1 (-> N/A)
lts/carbon -> v8.16.0 (-> N/A)
lts/dubnium -> v10.16.0
$ nvm use default  # 使用指定node版本
```

安装cnpm
```shell
$ npm --registry=https://registry.npm.taobao.org install cnpm -g
```

#### node.js 回调函数

> Node.js 异步编程的直接体现就是回调。
异步编程依托于回调来实现，但不能说使用了回调后程序就异步化了。
回调函数在完成任务后就会被调用，Node 使用了大量的回调函数，Node 所有 API 都支持回调函数。
例如，我们可以一边读取文件，一边执行其他命令，在文件读取完成后，我们将文件内容作为回调函数的参数返回。这样在执行代码时就没有阻塞或等待文件 I/O 操作。这就大大提高了 Node.js 的性能，可以处理大量的并发请求。

```js
let fs = require('fs');
let data = fs.readFileSync('input.txt');
console.log(data.toString());
console.log("程序执行结束!");
```
```shell
文件内容......
程序执行结束!
```

#### node.js 事件驱动

> Node.js 使用事件驱动模型，当web server接收到请求，就把它关闭然后进行处理，然后去服务下一个web请求。
当这个请求完成，它被放回处理队列，当到达队列开头，这个结果被返回给用户。
这个模型非常高效可扩展性非常强，因为 webserver 一直接受请求而不等待任何读写操作。（这也称之为非阻塞式IO或者事件驱动IO）
在事件驱动模型中，会生成一个主循环来监听事件，当检测到事件时触发回调函数。

#### node.js buffer 缓冲区

> JavaScript 语言自身只有字符串数据类型，没有二进制数据类型。
但在处理像TCP流或文件流时，必须使用到二进制数据。因此在 Node.js中，定义了一个 Buffer 类，该类用来创建一个专门存放二进制数据的缓存区。
在 Node.js 中，Buffer 类是随 Node 内核一起发布的核心库。Buffer 库为 Node.js 带来了一种存储原始数据的方法，可以让 Node.js 处理二进制数据，每当需要在 Node.js 中处理I/O操作中移动的数据时，就有可能使用 Buffer 库。原始数据存储在 Buffer 类的实例中。一个 Buffer 类似于一个整数数组，但它对应于 V8 堆内存之外的一块原始内存。

node.js目前支持
1. ascii - 仅支持 7 位 ASCII 数据。如果设置去掉高位的话，这种编码是非常快的。
2. utf8 - 多字节编码的 Unicode 字符。许多网页和其他文档格式都使用 UTF-8 。
3. utf16le - 2 或 4 个字节，小字节序编码的 Unicode 字符。支持代理对（U+10000 至 U+10FFFF）。
4. ucs2 - utf16le 的别名。
5. base64 - Base64 编码。
6. latin1 - 一种把 Buffer 编码成一字节编码的字符串的方式。
7. binary - latin1 的别名。
8. hex - 将每个字节编码为两个十六进制字符。


#### node.js stream 流

> Stream 是一个抽象接口，Node 中有很多对象实现了这个接口。例如，对http 服务器发起请求的request 对象就是一个 Stream，还有stdout（标准输出）。


#### node.js 模块

```js
// 导出一个方法
exports.world = function() {
  console.log('Hello World');
}

//hello.js 
function Hello() { 
    var name; 
    this.setName = function(thyName) { 
        name = thyName; 
    }; 
    this.sayHello = function() { 
        console.log('Hello ' + name); 
    }; 
}
module.exports = Hello;

```

#### node.js 全局对象

```js
// 当前正在执行的脚本的文件名
console.log( __filename );
// 当前执行脚本所在的目录
console.log( __dirname );
// 定时器, 只执行一次
let t = setTimeout(printHello, 2000);
clearTimeout(t);
// 全局函数在指定的毫秒(ms)数后执行指定函数(cb)
setInterval(printHello, 2000);
```

```js
// process: 一个全局变量，即 global 对象的属性。

```
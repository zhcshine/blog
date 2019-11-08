---
title: webpack入门基础
date: 2019-11-08 15:44:17
tags:
---

#### 安装

```shell
$ cnpm install webpack -g
$ cnpm install webpack-cli -g
```

#### webpack基础命令
```sell
# 目录结构
.
├── node_modules
├── package.json
└── src
    ├── index.html
    ├── js
    │   ├── app.js
    │   ├── module1.js
    │   ├── module2.js
    │   └── module3.js
    └── json
        └── data.json

4 directories, 7 files
```

```shell
# 测试环境
$ webpack src/js/app.js -o dist/js/app.js --mode=development
# 生产环境
$ webpack src/js/app.js -o dist/js/app.js --mode=production
```

webpack命令能：
> 够编译打包js和json文件

> 能将es6的模块化语法转换成浏览器能识别的语法

> 能压缩代码

webpack命令不能：
> 不能编译打包css、img等文件

> 不能将js的es6基本语法转化为es5以下语法

#### webpack配置

##### 配置文件 webpack.config.js
```js
// webpack.config.js
const {resolve} = require('path');  //node内置核心模块，用来设置路径。
module.exports = {
    entry: './src/js/app.js',   // 入口文件
    output: {                     // 输出配置
        filename: './js/bundle.js',      // 输出文件名
        path: resolve(__dirname, 'dist')   //输出文件路径配置
    },
    module: {
        rules: []
    },
    mode: 'development'   // development, production 二选一
};
```
```shell
$ webpack
```

##### webpack js语法检查
```shell
# 安装loader
cnpm install eslint-loader eslint --save-dev
```

```js
// webpack.config.js 配置loader
const {resolve} = require('path');  //node内置核心模块，用来设置路径。
module.exports = {
    entry: './src/js/app.js',   // 入口文件
    output: {                     // 输出配置
        filename: './js/bundle.js',      // 输出文件名
        path: resolve(__dirname, 'dist')   //输出文件路径配置
    },
    module: {
        rules: [
            {
                test: /\.js$/,  //只检测js文件
                exclude: /node_modules/,  //排除node_modules文件夹
                enforce: "pre",  //提前加载使用
                use: { //使用eslint-loader解析
                    loader: "eslint-loader"
                }
            }
        ]
    },
    mode: 'development'   // development, production 二选一
};
```
package.json 添加eslintConfig
```json
{
  "name": "nodelearn",
  "version": "1.0.0",
  "description": "测试",
  "main": "1.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "eslint": "^6.6.0",
    "eslint-loader": "^3.0.2"
  },
  "eslintConfig": {
    "parserOptions": {
      "ecmaVersion": 8,
      "sourceType": "module"
    }
  }
}
```

##### webpack js语法转换
```shell
$ cnpm install babel-loader @babel/core @babel/preset-env --save-dev
```

```js
const {resolve} = require('path');  //node内置核心模块，用来设置路径。
module.exports = {
    entry: './src/js/app.js',   // 入口文件
    output: {                     // 输出配置
        filename: './js/bundle.js',      // 输出文件名
        path: resolve(__dirname, 'dist')   //输出文件路径配置
    },
    module: {
        rules: [
            {
                test: /\.js$/,  //只检测js文件
                exclude: /node_modules/,  //排除node_modules文件夹
                enforce: "pre",  //提前加载使用
                use: { //使用eslint-loader解析
                    loader: "eslint-loader"
                }
            },
            {
                test: /\.js$/,
                exclude: /node_modules/,
                use: {
                    loader: "babel-loader",
                    options: {
                        presets: ['@babel/preset-env']
                    }
                }
            }
        ]
    },
    mode: 'development'   // development, production 二选一
};
```
##### webpack打包html文件

```shell
$ cnpm install html-webpack-plugin --save-dev
$ cnpm install clean-webpack-plugin --save-dev
$ cnpm install html-loader --save-dev
```

```js
// webpack.config.js
// 引入webpack
const webpack = require('webpack');
// 配置插件
const HtmlWebpackPlugin = require('html-webpack-plugin'); //引入插件html-webpack-plugin
const {CleanWebpackPlugin} = require('clean-webpack-plugin');  //引入清除文件插件
const {resolve} = require('path');  //node内置核心模块，用来设置路径。
module.exports = {
    entry: './src/js/app.js',   // 入口文件
    output: {                     // 输出配置
        filename: './js/bundle.js',      // 输出文件名
        path: resolve(__dirname, 'dist')   //输出文件路径配置
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html',
            filename: 'index.html',// 默认情况下生成的 html 文件叫 index.html
            minify: {
                collapseWhitespace: true, //把生成的 index.html 文件的内容的没用空格去掉，减少空间
            },
            hash: true, //为了更好的 cache，可以在文件名后加个 hash。
        }),
        new CleanWebpackPlugin(), //实例化，参数为目录
    ],
    module: {
        rules: [
            {
                test: /\.js$/,  //只检测js文件
                exclude: /node_modules/,  //排除node_modules文件夹
                enforce: "pre",  //提前加载使用
                use: { //使用eslint-loader解析
                    loader: "eslint-loader"
                }
            },
            {
                test: /\.js$/,
                exclude: /node_modules/,
                use: {
                    loader: "babel-loader",
                    options: {
                        presets: ['@babel/preset-env']
                    }
                }
            }
        ]
    },
    mode: 'development'   // development, production 二选一
};

```
##### webpack 自动编译打包运行

```shell
$ cnpm install webpack --save-dev
$ cnpm install webpack-dev-server --save-dev
$ cnpm install webpack-cli --save-dev 
```

```js
// webpack.config.js 添加devtool, devServer, plugins
// 引入webpack
// 引入webpack
const webpack = require('webpack');
// 配置插件
const HtmlWebpackPlugin = require('html-webpack-plugin'); //引入插件html-webpack-plugin
const {CleanWebpackPlugin} = require('clean-webpack-plugin');  //引入清除文件插件
const {resolve} = require('path');  //node内置核心模块，用来设置路径。
module.exports = {
    entry: './src/js/app.js',   // 入口文件
    output: {                     // 输出配置
        filename: './js/bundle.js',      // 输出文件名
        path: resolve(__dirname, 'dist')   //输出文件路径配置
    },
    devtool: 'inline-source-map',  // 将编译后的代码映射回原始源代码，更容易地追踪错误和警告
    devServer: {
        contentBase: './dist',  //项目根路径
        hot: true,  //开启热模替换功能
        open: true  //自动打开浏览器
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html',
            filename: 'index.html',// 默认情况下生成的 html 文件叫 index.html
            minify: {
                collapseWhitespace: true, //把生成的 index.html 文件的内容的没用空格去掉，减少空间
            },
            hash: true, //为了更好的 cache，可以在文件名后加个 hash。
        }),
        new CleanWebpackPlugin(), //实例化，参数为目录
        new webpack.NamedModulesPlugin(),
        new webpack.HotModuleReplacementPlugin()
    ],
    module: {
        rules: [
            {
                test: /\.js$/,  //只检测js文件
                exclude: /node_modules/,  //排除node_modules文件夹
                enforce: "pre",  //提前加载使用
                use: { //使用eslint-loader解析
                    loader: "eslint-loader"
                }
            },
            {
                test: /\.js$/,
                exclude: /node_modules/,
                use: {
                    loader: "babel-loader",
                    options: {
                        presets: ['@babel/preset-env']
                    }
                }
            }
        ]
    },
    mode: 'development'   // development, production 二选一
};


```

package.json添加start，dev命令

```json
{
  "name": "nodelearn",
  "version": "1.0.0",
  "description": "测试",
  "main": "1.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack-dev-server",
    "dev": "webpack-dev-server"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "@babel/core": "^7.7.2",
    "@babel/preset-env": "^7.7.1",
    "babel-loader": "^8.0.6",
    "eslint": "^6.6.0",
    "eslint-loader": "^3.0.2",
    "webpack-dev-server": "^3.9.0"
  },
  "eslintConfig": {
    "parserOptions": {
      "ecmaVersion": 8,
      "sourceType": "module"
    }
  }
}

```

##### 生产环境

项目目录下新建config/webpack.dev.js, config/webpack.prod.js, 删除webpack.config.js

```js
// webpack.dev.js
// 引入webpack
const webpack = require('webpack');
// 配置插件
const HtmlWebpackPlugin = require('html-webpack-plugin'); //引入插件html-webpack-plugin
const {CleanWebpackPlugin} = require('clean-webpack-plugin');  //引入清除文件插件
const {resolve} = require('path');  //node内置核心模块，用来设置路径。
module.exports = {
    entry: './src/js/app.js',   // 入口文件
    output: {                     // 输出配置
        filename: './js/bundle.js',      // 输出文件名
        path: resolve(__dirname, '../dist')   //输出文件路径配置
    },
    devtool: 'inline-source-map',  // 将编译后的代码映射回原始源代码，更容易地追踪错误和警告
    devServer: {
        contentBase: './dist',  //项目根路径
        hot: true,  //开启热模替换功能
        open: true  //自动打开浏览器
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html',
            filename: 'index.html',// 默认情况下生成的 html 文件叫 index.html
            minify: {
                collapseWhitespace: true, //把生成的 index.html 文件的内容的没用空格去掉，减少空间
            },
            hash: true, //为了更好的 cache，可以在文件名后加个 hash。
        }),
        new CleanWebpackPlugin(),
        new webpack.NamedModulesPlugin(),
        new webpack.HotModuleReplacementPlugin()
    ],
    module: {
        rules: [
            {
                test: /\.js$/,  //只检测js文件
                exclude: /node_modules/,  //排除node_modules文件夹
                enforce: "pre",  //提前加载使用
                use: { //使用eslint-loader解析
                    loader: "eslint-loader"
                }
            },
            {
                test: /\.js$/,
                exclude: /node_modules/,
                use: {
                    loader: "babel-loader",
                    options: {
                        presets: ['@babel/preset-env']
                    }
                }
            }
        ]
    },
    mode: 'development'   // development, production 二选一
};
```
```js
// 引入webpack
// webpack.prod.js
const webpack = require('webpack');
// 配置插件
const HtmlWebpackPlugin = require('html-webpack-plugin'); //引入插件html-webpack-plugin
const {CleanWebpackPlugin} = require('clean-webpack-plugin');  //引入清除文件插件
const {resolve} = require('path');  //node内置核心模块，用来设置路径。
module.exports = {
    entry: './src/js/app.js',   // 入口文件
    output: {                     // 输出配置
        filename: './js/[name].[hash:8].js',      // 输出文件名
        path: resolve(__dirname, '../dist')   //输出文件路径配置
    },
    devtool: 'inline-source-map',  // 将编译后的代码映射回原始源代码，更容易地追踪错误和警告
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html',
            filename: 'index.html',// 默认情况下生成的 html 文件叫 index.html
            minify: {
                collapseWhitespace: true, //把生成的 index.html 文件的内容的没用空格去掉，减少空间
            },
            hash: true, //为了更好的 cache，可以在文件名后加个 hash。
        }),
        new CleanWebpackPlugin(),
        new webpack.NamedModulesPlugin(),
        new webpack.HotModuleReplacementPlugin()
    ],
    module: {
        rules: [
            {
                test: /\.js$/,  //只检测js文件
                exclude: /node_modules/,  //排除node_modules文件夹
                enforce: "pre",  //提前加载使用
                use: { //使用eslint-loader解析
                    loader: "eslint-loader"
                }
            },
            {
                test: /\.js$/,
                exclude: /node_modules/,
                use: {
                    loader: "babel-loader",
                    options: {
                        presets: ['@babel/preset-env']
                    }
                }
            }
        ]
    },
    mode: 'production'   // development, production 二选一
};
```

package.json
```json
{
  "name": "nodelearn",
  "version": "1.0.0",
  "description": "测试",
  "main": "1.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack-dev-server --config ./config/webpack.dev.js",
    "dev": "webpack-dev-server --config ./config/webpack.dev.js",
    "build": "webpack --config ./config/webpack.prod.js"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "@babel/core": "^7.7.2",
    "@babel/preset-env": "^7.7.1",
    "babel-loader": "^8.0.6",
    "clean-webpack-plugin": "^3.0.0",
    "eslint": "^6.6.0",
    "eslint-loader": "^3.0.2",
    "html-loader": "^0.5.5",
    "html-webpack-plugin": "^3.2.0",
    "webpack": "^4.41.2",
    "webpack-cli": "^3.3.10",
    "webpack-dev-server": "^3.9.0"
  },
  "eslintConfig": {
    "parserOptions": {
      "ecmaVersion": 8,
      "sourceType": "module"
    }
  }
}

```

项目目录
```shell
├── config
│   ├── webpack.dev.js
│   └── webpack.prod.js
├── package.json
└── src
    ├── index.html
    ├── js
    │   ├── app.js
    │   ├── module1.js
    │   ├── module2.js
    │   └── module3.js
    └── json
        └── data.json
```
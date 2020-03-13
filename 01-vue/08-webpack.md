## 1. 概念

本质上，webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle。



## 2. webpack安装

前提条件：安装webpack之前必须确保安装了Node.js，Node.js里自带了软件包管理工具npm

### 2.1 本地安装

要安装最新版本或者特定版本，请使用以下命令：

~~~shell
npm install --save-dev webpack
npm install --save-dev webpack@<version>
~~~

如果需要安装webpack4+版本，还需要安装CLI

~~~shell
npm install --save-dev webpack-cli
~~~

最新的webpack版本是：

[![GitHub release](https://img.shields.io/npm/v/webpack.svg?label=webpack&style=flat-square&maxAge=3600)](https://github.com/webpack/webpack/releases)

当你在本地安装 webpack 后，你能够从 node_modules/.bin/webpack 访问它的 bin 版本。一般来说，我们推荐这种安装方式。



### 2.2 全局安装

~~~shell
npm install --global webpack
或者
npm install webpack -g
~~~

不推荐全局安装 webpack。这会将你项目中的 webpack 锁定到指定版本，并且在使用不同的 webpack 版本的项目中，可能会导致构建失败。




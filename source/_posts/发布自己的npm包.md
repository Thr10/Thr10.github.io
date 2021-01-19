---
title: 发布自己的npm包
date: 2021-01-12 16:01:21
tags:
---

## 前言
#### 内容概括
由于工作中遇到了node_modules依赖包升级后导致编译报错的问题，想彻底梳理npm的包管理机制，并且尝试自己发布npm包，以及构建他们的依赖关系。该篇blog就是一个探索的过程记录，在探索过程中对他人教学的总结，以及遇到问题的一些解决办法。

#### 计划
1. 搞清楚 `npm install` 之后到底发了什么，以及 `package-lock.json`、`package.json` 文件的内容描述
2. 发布npm包
3. 构建包之间不同的依赖关系，观察升级包和依赖包版本时，`package-lock.json`、`package.json` 的变化。

## 探索过程
### npm install 之后发生了什么？
#### 依赖包的嵌套关系
早期的npm以递归的方式按照 `package.json` 中的结构将依赖包安装到 `node_modules`，例如下面的结构(包名、版本号、以及其依赖纯属虚构)
``````
{
  "name": "my-app",
  "dependencies": {
    "webpack": "^4.8.5",
    "logic": "^2.1.2",
  }
}
``````
其中logic包还有自己的依赖关系
``````
{
  "name": "logic",
  "dependencies": {
    "base64-js": "^1.1.2",
    "lodash": "^3.10.1",
  }
}
``````
那么npm install 之后，在 `node_modules` 中的结构应该是
![img1](/images/blog-npm-1.png)

虽然这样的设计会让node_modules中的结构和`package.json`中的结构对应，且保证每次安装结构都是相同的。但当依赖关系的层级非常深的时候，如下图
![img2](/images/blog-npm-2.png)
这会引发如下问题
+ 不同层级的依赖引入同一个模块，会导致大量冗余
+ `Windows` 系统中，路径最大长度260个字符，嵌套结构过深会导致路径问题

## 使用扁平化管理
npm 3.x 版本做了一个更新，将嵌套结构改为扁平化结构，安装模块时，无论是依赖包，还是依赖包的依赖包，都优先安装到 `node_modules` 根目录中。依然是上面的结构，那么npm install 后得到的`node_modules` 结构如下:
![](/images/blog-npm-3.png)
如果logic依赖不同版本的 base64-js ，而 `node_modules` 根目录下已经存在一个 base64-js 了，新的依赖包会加载到 logic 文件夹下面的 `node_modules` 中。
![](/images/blog-npm-4.png)
一个模块在引入依赖包时，会按照下面的顺序逐层查找：
+ 当前目录下
+ 当前模块的 `node_modules` 中
+ 上一级模块下的  `node_modules` 中
....
+ 直到全局路径下的  `node_modules`

这样的扁平处理依然会有问题，假设依赖logic2模块，而logic2依赖 base64-js 的版本和logic依赖的base64-js版本不同，如下所示
![](/images/blog-npm-5.png)

当 my-app 不依赖 base64-js@^1.1.2 时，logic和logic2在`package.json`中的顺序会影响最 `node_modules` 的结构
![](/images/blog-npm-6.png)

且一般的依赖包都只会锁定大版本号，当小版本升级时，依然有可能影响 `node_modules` 的结构，不确定的`node_modules`结构会对开发带来一些未知的问题。


### 发布自己的 npm 包
### npm包管理
#### npm历史版本




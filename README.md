# 如何利用 Electron 开发一个桌面 APP

## 什么是 Electron？

Electron是由Github开发，用HTML，CSS和JavaScript来构建跨平台桌面应用程序的一个开源库。

Electron通过将Chromium和Node.js合并到同一个运行时环境中，并将其打包为Mac，Windows和Linux系统下的应用来实现这一目的。

它还有其它一些特性：

- 自动更新 —— 应用支持自动更新

- 原生菜单和通知 —— 可以创建原生应用菜单和上下文菜单

- 应用崩溃报告 —— 可以将崩溃报告提交到远程服务器

- 调试和分析 —— Chrominum 的内容模块可以发现性能瓶颈和缓慢的操作。你也可以在应用中使用自己喜欢的 Chrome 开发者工具。

- Windows installer —— 可以快速便捷地创建安装包。

官网：https://electronjs.org/

## Electron 应用结构
### 主进程和渲染器进程
Electron 运行 package.json 的 main 脚本的进程被称为主进程。 在主进程中运行的脚本通过创建web页面来展示用户界面。 一个 Electron 应用总是有且只有一个**主进程**。

由于 Electron 使用了 Chromium 来展示 web 页面，所以 Chromium 的多进程架构也被使用到。 每个 Electron 中的 web 页面运行在它自己的**渲染进程**中。

在普通的浏览器中，web页面通常在一个沙盒环境中运行，不被允许去接触原生的资源。 然而 Electron 的用户在 Node.js 的 API 支持下可以在页面中和操作系统进行一些底层交互。
### 主进程和渲染进程之间的区别
主进程使用 BrowserWindow 实例创建页面。 每个 BrowserWindow 实例都在自己的渲染进程里运行页面。 当一个 BrowserWindow 实例被销毁后，相应的渲染进程也会被终止。

主进程管理所有的web页面和它们对应的渲染进程。 每个渲染进程都是独立的，它只关心它所运行的 web 页面。

在页面中调用与 GUI 相关的原生 API 是不被允许的，因为在 web 页面里操作原生的 GUI 资源是非常危险的，而且容易造成资源泄露。 如果你想在 web 页面里使用 GUI 操作，其对应的渲染进程必须与主进程进行通讯，请求主进程进行相关的 GUI 操作。
### 使用Electron的API
Electron在主进程和渲染进程中提供了大量API去帮助开发桌面应用程序， 在主进程和渲染进程中，你可以通过require的方式将其包含在模块中以此，获取Electron的API

```
const electron = require('electron')
```
所有Electron的API都被指派给一种进程类型。 许多API只能被用于主进程中，有些API又只能被用于渲染进程，又有一些主进程和渲染进程中都可以使用。 每一个API的文档都将说明可以在哪种进程中使用该API。

Electron中的窗口是使用BrowserWindow类型创建的一个实例， 它只能在主进程中使用。

```
// 这样写在主进程会有用，但是在渲染进程中会提示'未定义'
  const { BrowserWindow } = require('electron')
  
  const win = new BrowserWindow()
```
因为进程之间的通信是被允许的, 所以渲染进程可以调用主进程来执行任务。 Electron通过remote模块暴露一些通常只能在主进程中获取到的API。 为了在渲染进程中创建一个BrowserWindow的实例，我们通常使用remote模块为中间件：


```
//这样写在渲染进程中时行得通的，但是在主进程中是'未定义'
  const { remote } = require('electron')
  const { BrowserWindow } = remote
  
  const win = new BrowserWindow()
```
### 使用 Node.js 的 API

Electron同时在主进程和渲染进程中对Node.js 暴露了所有的接口。 这里有两个重要的定义：

1. 所有在Node.js可以使用的API，在Electron中同样可以使用。 在Electron中调用如下代码是有用的：

```
const fs = require('fs')
const root = fs.readdirSync('/')
  
// 这会打印出磁盘根级别的所有文件
// 同时包含'/'和'C:\'。
console.log(root)
```

2. 你可以在你的应用程序中使用Node.js的模块。 选择您最喜欢的 npm 模块。

例如，在你的应用程序中要使用官方的AWS SDK，你需要首先安装它的依赖：


```
npm install --save aws-sdk
```

然后在你的Electron应用中，通过require引入并使用该模块，就像构建Node.js应用程序那样：

```
// 准备好被使用的S3 client模块
  const S3 = require('aws-sdk/clients/s3')
```

## 开始
将 Electron Quick Start 这个 Git 库clone到本地建好的文件夹下，我们会基于 Electron Quick Start 来创建自己的项目。

```
# clone仓库
$ git clone https://github.com/electron/electron-quick-start

# 进入这个仓库
$ cd electron-quick-start

# 安装依赖并运行
$ npm install && npm start
```
运行成功后，你会看到一个像浏览器窗口的应用打开。
![image](http://p5tstjsfi.bkt.clouddn.com/electron-share1.png)

它也确实是一个浏览器窗口！我们可以像可以在应用中使用 Chrome 开发者工具，这个工具的用法跟在浏览器中的开发者工具是一样的，调用方法：

```
mainWindow.webContents.openDevTools()
```
## Electron 应用程序架构
![image](http://p5tstjsfi.bkt.clouddn.com/electron-share2.png)

这是一个基本的文件结构：

electron-quick-start

- index.html
- main.js
- package.json
- render.js

文件结构类似于我们创建网页的结构。

我们有：

index.html 是一个 HTML5 页面，它具有很重要的作用：提供画布

main.js 创建窗口并处理系统事件

package.json 是应用的启动脚本。它包含了应用的信息，在主进行中运行

render.js 处理应用的渲染进程

## 添加响应用户输入功能
为了让应用响应输入，我们必须定义一个元素来捕捉事件，然后触发期望的动作。

1. 我们先创建一个具有特殊名称的若干 audio 元素，对应于按键。然后我们会使用一个 switch 语句来定位按下的键，播放与之关联的声音。下载这个[压缩包](https://neutrondev.com/wp-content/uploads/2017/05/sounds.zip?x77671)，这是所有的声音文件。
打开 index.html 文件，创建一个 <audio> 元素，在我们的应用中加入声音：

```
<div class="audio">
    <audio id="A" src="sounds/A.mp3" preload="auto"></audio>
</div>
```
preload=”auto” 用于告诉应用在页面加载的时候就加载完整的声音文件。index.html 是应用的主文件，所有声音都会在应用启动的时候加载。

2. 然后创建一个名为 sounds 的目录，并将所有声音文件解压到这个目录中。


3. 创建一个叫 functions.js 的新文件，并在 index.html 中通过 require 引用它。
```
// document 对象是应用的主窗口
document.onkeydown = function(e) {
    // 根据 Unicode 值来判断按键。
    switch (e.keyCode) {
        // 播放相应的声音
        case 65:
            document.getElementById('A').play();
            break;
        // 抛出 “not found”(在控制台查看)
        default:
            console.log("Key is not found!");
    }
};
```

```
require('./functions.js')
```
4. 将所有声音文件0-9a-z都引入并创建对应的audio

5. 运行 npm start 来启动应用。

## 完善
应用程序的功能已经完成，但它尚不完善。比如，可以在 index.html中修应用的标题和主窗口的内容。或者可以给应用设计炫丽的色彩、使用漂亮的图片。
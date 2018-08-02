# 如何利用 Electron 开发一个桌面 APP

## 什么是 Electron？

Electron 是一个基于 Chrominum 和 Node.js 的跨平台桌面应用框架。

在这个框架中很容易构建基于 HTML、CSS 和 JavaScript 技术的跨平台应用。构建出来的应用会很好地兼容 Mac、Windows 和 Linux 操作系统。

它还有其它一些特性：

- 自动更新 —— 应用支持自动更新

- 原生菜单和通知 —— 可以创建原生应用菜单和上下文菜单

- 应用崩溃报告 —— 可以将崩溃报告提交到远程服务器

- 调试和分析 —— Chrominum 的内容模块可以发现性能瓶颈和缓慢的操作。你也可以在应用中使用自己喜欢的 Chrome 开发者工具。

- Windows installer —— 可以快速便捷地创建安装包。

官网：https://electronjs.org/

## 开始
按照下面的介绍将 Electron Quick Start 这个 Git 库克隆到计算机上。

我们会基于 Electron Quick Start 来创建自己的软件。

```
# 克隆示例项目的仓库
$ git clone https://github.com/electron/electron-quick-start

# 进入这个仓库
$ cd electron-quick-start

# 安装依赖并运行
$ npm install && npm start
```
完成上面的步骤之后，你会看到一个像浏览器窗口的应用打开。它确实是一个浏览器窗口！
![image](http://p5tstjsfi.bkt.clouddn.com/electron-share1.png)

Quick-Start Electron 应用的主窗口

正如我之前所说，你可以在应用中使用 Chrome 开发者工具，这个工具的用法跟在浏览器中的开发者工具一样，再赞一个！

## Electron 应用程序架构
![image](http://p5tstjsfi.bkt.clouddn.com/electron-share2.png)
新应用的目录和文件结构。

我们有一个基本的文件结构：

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

## 添加功能
为了让应用响应输入，我们必须定义一个元素来捕捉事件，然后触发期望的动作。

为此，我们会创建一个具有特殊名称的若干 audio 元素，对应于按键。然后我们会使用一个 switch 语句来定位按下的键，播放与之关联的声音。

下载这个[压缩包](https://neutrondev.com/wp-content/uploads/2017/05/sounds.zip?x77671)，它包含了我们要使用的所有声音文件。很快就会用到！

我们会打开 index.html 文件，创建一个 <audio> 元素，在我们的应用中加入声音。

在 <body> 元素内部，创建一个 div 元素，将其 class 属性设置为 audio。

在刚刚创建的 div 元素，创建 <audio> 元素，将其 ID 命名为 “A”，source 属性设置为 “sounds/A.mp3”，preload 属性设置为 “auto”。

preload=”auto” 用于告诉应用在页面加载的时候就加载完整的声音文件。index.html 是应用的主文件，所有声音都会在应用启动的时候加载。

下面是代码：

```
<div class="audio">
<audio id="A" src="sounds/A.mp3" preload="auto"></audio>
</div>
```

现在 <audio> 指向一个未知的文件。我们要创建一个名为 soudes 的目录，并将所有声音文件解压到这个目录中。

非常好！现在唯一缺少的是 JavaScriopt 代码。

创建一个叫 functions.js 的新文件，并在 index.html 中通过 require 引用它，这样应用运行的时候才会执行 JS 代码。

在示例的 require(./renderer.js') 下载添加这样一行：


```
require('./functions.js')
```
打开 functions.js 文件并将下面的代码添加到文件中。我稍后解释这段代码。
```
document.onkeydown = function(e) {
    switch (e.keyCode) {
        case 65:
            document.getElementById('A').play();
            break;
        default:
            console.log("Key is not found!");
    }
};
```
打开 Bash 或者终端窗口，确保当前是在项目目录下，运行 npm start 来启动应用。

调整扬声器的音量并敲下按键。

JS 代码非常简单明了。

我们使用了 document 对象上的 onkeydown 事件，在这里找到被访问的元素。记住，document 对象是应用的主窗口。

我们在在匿名函数中使用了 switch 语句，根据 Unicode 值来判断按键。

如果找到按键对应的 Unicode 值，就会播放相应的声音，否则抛出 “not found” 错误。这个错误要在控制台中去找。

你可能注意到了，我们的声音文件包含了 A-Z 和 0-9 这些键，把它们都用起来。

在 index.html 中为每个有对应声音文件的键都创建一个 <audio> 元素并修改functions.js文件。

**大功告成！**
## 完善
应用程序的功能已经完成，但它尚不完善。

比如，可以在 index.html 中修应用的标题和主窗口的内容。

此外，这个应用没有设计炫丽的色彩，也没有使用漂亮的图片。

充分发挥你的想像，找出改进应用设计的方法。

代码也不完美，我们有很多相同的代码需要优化以减少代码行数，至少看起来不那么难受。

重复代码真不是好做法！
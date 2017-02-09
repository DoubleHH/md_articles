# React Native教程

注：Tom Elliot同学已将本篇教程内所讲的React Native版本更新到0.22。原始的的教程是iOS团队成员Colin Eberhardt同学所写。

在这篇React Native教程中你会学习到一个能同时编译iOS及Android原生应用的框架，这个框架背后的理念和Facebook非常受欢迎用于搭建声明式用户界面的[React Javascript Framework](https://facebook.github.io/react/)是一致的。

这些年来有许许多多使用JavaScript创建iOS应用的框架（比如[PhoneGap](http://phonegap.com/), [Titanium](http://www.appcelerator.com/mobile-app-development-products/)），那么React Native有何特殊之处？

1. （和PhoneGap不同）使用React Native你的应用逻辑的编写及运行都使用JavaScript，然而应用的界面全部都是原生的；因此你不会存在像HTML5一样的折中的界面情况；
2. 另外（不像Titanium），React引入了一个新颖及完全高度函数式方案来构造用户界面。简言之，应用界面通过当前应用状态的一个函数就简洁地表达了；

React Native最重要的点是它将[React](http://facebook.github.io/react/)编程模型引入到手机开发领域。它的目的不是成为一个跨平台，一次编写运行于任何平台的工具。它目的是学习一次，可以在任何其他地方编写。教程只包含iOS部分，但一旦你学会了这里介绍的理念，你就能运用这些知识很快创建一个安卓的应用。

如果你只用过Objective-C或者Swift，你可能不会那么兴奋，因为这里使用的是JavaScript。但，作为一个Swift开发者，教程第二部分可能会挑起你的兴趣。

通过学习Swift，毫无疑问你已经开始学习新的更多的一些函数方法来进行编解码，以及一些技术来实现一些转换或者保持不变性。但，React Native内创建界面的方式和Objective-C语言创建方式非常类似，它仍然是基于UIKit且非常重要。

使用一些诸如虚拟的文档对象模型（DOM）及协调等令人好奇的概念，React把函数式编程直接应用到了界面层。

这篇教程主要让你看看使用React Native构造一个搜索UK资产表应用的过程：

![](http://www.raywenderlich.com/wp-content/uploads/2015/03/PropertyFinder.png)

如果你从来没有写过JavaScript代码，不要担心，这篇文章将为你讲解每一步的代码。React Native使用类似CSS样式的属性来处理界面样式，这些属性都非诚易于读和理解的。但如果你想了解CSS，你可以参考这篇文章[Mozilla Developer Network reference](https://developer.mozilla.org/en-US/docs/Web/CSS)

想学习更多吗？那么继续吧~

# 开始

React Native使用[Node.js](https://nodejs.org/)---JavaScript运行时来构建JavaScript代码。如果你还没装Node.js，是时候装上了。

首先[安装Homebrew](http://brew.sh/)---根据Homebrew主页上的说明，然后在终端运行以下代码来装Node.js：

> brew install node

下一步，使用Homebrew安装[watchman（看门狗）](https://facebook.github.io/watchman/)，它是来自于Facebook编写的一个监听器。

> brew install watchman

它是 React Native 用来监听代码变化而重新编译代码的。相当于你在Xcode每次保存文件时都会重新编译一次。

下一步使用npm安装 React Native 命令行界面(CLI)工具：

> npm install -g react-native-cli

这里使用[Node Package Manager(Node包管理器)](https://www.npmjs.com/)获取CLI并在全局范围安装CLI；npm在功能上跟[CocoaPods](https://cocoapods.org/),[Carthage](https://github.com/Carthage/Carthage)差不多，只不过npm用来安装Node.js的包。

> 如果你想深入React Native框架，在GitHub上可以获取到它的[源代码](https://github.com/facebook/react-native)。

在终端切到你用于开发React Native应用的文件夹，并执行以下命令构建工程：

> react-native init PropertyFinder

这个命令创建了一个包含能让你编译执行React Native应用所需要的任何东西的初始工程。

> 如果你抱怨Node的版本，那么你需要保证brew安装的node是React Native使用的，在终端运行命令`brew link --overwrite node`即可。

若你查看刚创建的工程文件夹，你会发现node_modules目录，这里包含了React Native框架。你也会发现 index.ios.js 文件，该文件是CLI工具创建的iOS核心应用文件。还有一个iOS目录，包含了Xcode工程及一小部分用于启动应用的代码。另外，相对应的也有一个安卓的配置，但本篇教程里不会涉及。

打开Xcode工程文件，编译并运行。模拟器将会启动并显示如下欢迎页面：

![Welcome to React Native~](http://www.raywenderlich.com/wp-content/uploads/2016/02/ReactNative-Starter-281x500.png)

> 此时编译时Xcode可能会有一些警告，请不要担心，React Native团队已经知道这些警告，且在未来的版本会修复:]

你应该注意到了弹出了一个终端，里面内容大概这样：

~~~
 ┌──────────────────────────────────────────────┐
 │  Running packager on port 8081.                                            │
 │                                                                            │
 │  Keep this packager running while developing on any JS projects. Feel      │
 │  free to close this tab and run your own packager instance if you          │
 │  prefer.                                                                   │
 │                                                                            │
 │  https://github.com/facebook/react-native                                  │
 │                                                                            │
 └──────────────────────────────────────────────┘
Looking for JS files in
   /Users/tomelliott/Desktop/Scratch/PropertyFinder
 
[6:15:40 PM] <START> Building Dependency Graph
[6:15:40 PM] <START> Crawling File System
[6:15:40 PM] <START> Loading bundles layout
[6:15:40 PM] <END>   Loading bundles layout (0ms)
[Hot Module Replacement] Server listening on /hot
 
React packager ready.
 
[6:15:41 PM] <END>   Crawling File System (747ms)
[6:15:41 PM] <START> Building in-memory fs for JavaScript
[6:15:42 PM] <END>   Building in-memory fs for JavaScript (653ms)
[6:15:42 PM] <START> Building in-memory fs for Assets
[6:15:42 PM] <END>   Building in-memory fs for Assets (277ms)
[6:15:42 PM] <START> Building Haste Map
[6:15:42 PM] <START> Building (deprecated) Asset Map
[6:15:42 PM] <END>   Building (deprecated) Asset Map (49ms)
[6:15:42 PM] <END>   Building Haste Map (400ms)
[6:15:42 PM] <END>   Building Dependency Graph (2094ms)
~~~

这是React Native的在节点下运行的打包器。你很容易了解它做的事情。

不要关闭该终端，让它一直在后台运行，如果你不小心关闭了，只需要停止运行，再一次通过Xcode运行起来即可。

> 注：在本篇教程里写大量JavaScript代码之前的最后一件事情：Xcode并不是一个很好的写JavaScript代码的工具。我使用的是[Sublime Text](http://www.sublimetext.com/)，这是一个便宜又功能强大的编辑器。使用[atom](https://atom.io/)，[brackets](http://brackets.io/)或者其他轻量级的编辑器也同样能完成工作。

# Hello React Native

在开始资产搜索应用前，我们将先创建一个简单的Hello World的应用程序。随着讲解的进行，你将会认识到各式各样的组件及概念。

在你选择好的编辑器内打开index.ios.js文件，删除当前的内容
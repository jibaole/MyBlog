---
title: ubuntu下reactnative开发环境的配置
tags:
  - reactnative
categories:
  - Mobile development
toc: true
date: 2016-10-21 10:20:16
description: 
feature:
---


>尝试在　Ubuntu 14.04　上面进行 react-native 的开发, 安装官方文档（https://facebook.github.io/react-native/docs/getting-started.html）进行配置,记录配置流程。

#### 安装 node.js
在官网（https://nodejs.org/en/）下载源码安装即可, 安装后查看版本
``` bash
$ node -v
```

#### 安装 watchman
文档（https://facebook.github.io/watchman/docs/install.html）
<!-- more -->
#### 安装依赖
``` bash
$ sudo apt-get install autoconf  automake python-dev
# 安装 watchman, 如果出错, 查看安装依赖的详细文档
$ git clone https://github.com/facebook/watchman.git
$ cd watchman
$ git checkout v4.3.0  # the latest stable release
$ ./autogen.sh
$ ./configure
$ make
$ sudo make install
```

#### 安装 react-native
``` bash
$ npm install -g react-native-cli
```

#### 创建react-native 项目
``` bash
$ react-native init AwesomeProject
```

#### 运行
启动 server, 如果没有启动server , 会报错 React Native: ReferenceError: Can't find variable: require (line 1 in the generated bundle)
``` bash
$ react-native start 
```
![](http://www.linuxdiyf.com/linux/uploads/allimg/151227/2-15122F94H4194.jpg)
编译apk,并运行到模拟器, 需要配置好Android开发的环境变量(ANDROID_HOME, GRADLE_HOME )
``` bash
$ react-native run-android
```
![](http://www.linuxdiyf.com/linux/uploads/allimg/151227/2-15122F94IAW.jpg)
做一些修改
``` js
/**
* Sample React Native App
* https://github.com/facebook/react-native
*/
'use strict';
var React = require('react-native');
var {
AppRegistry,
StyleSheet,
Text,
View,
} = React;
var AwesomeProject = React.createClass({
render: function() {
return (
<View style={styles.container}>
<Text style={styles.welcome}>
Hanks,  Welcome to React Native!
</Text>
<Text style={styles.instructions}>
To get started, edit index.android.js
</Text>
<Text style={styles.reply}>
Yep, I do!
</Text>
<Text style={styles.instructions}>
Shake or press menu button for dev menu
</Text>
</View>
);
}
});
var styles = StyleSheet.create({
container: {
flex: 1,
justifyContent: 'center',
alignItems: 'center',
backgroundColor: '#F5FCFF',
},
welcome: {
fontSize: 20,
textAlign: 'center',
margin: 10,
},
instructions: {
textAlign: 'center',
color: '#333333',
marginBottom: 5,
},
// 添加style
reply:{
color: '#e8801b',
fontSize: 20
}
});
AppRegistry.registerComponent('AwesomeProject', () => AwesomeProject);
```
再次reload js, 可以通过菜单调出选项。
![](http://www.linuxdiyf.com/linux/uploads/allimg/151227/2-15122F94K2221.jpg)

转自：http://www.linuxdiyf.com/linux/16834.html

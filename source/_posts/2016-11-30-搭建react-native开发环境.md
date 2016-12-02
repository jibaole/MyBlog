---
title: 搭建react native开发环境
tags:
  - reactnative
categories:
  - Mobile development
toc: true
date: 2016-11-30 10:12:40
description: 
feature:
---

>Development OS: macOS 
Mobile OS: Android

#### Installing Dependencies 
``` bash
brew install node
brew install watchman
```

#### The React Native CLI 
``` bash
npm install -g react-native-cli
```
<!-- more -->
#### Android Development Environment 
1. Download and install Android Studio 
>https://developer.android.com/studio/install.html
Android Studio requires the Java Development Kit (JDK), version 1.8 or higher. You can type javac -version in a terminal to see what version you have, if any.

2. Install the AVD and HAXM 
3. Install the Android 6.0 (Marshmallow) SDK 
4. Set up the ANDROID_HOME environment variable 
Add the following lines to your ~/.bashrc (or equivalent) config file:
```
export ANDROID_HOME=~/Library/Android/sdk
export PATH=${PATH}:${ANDROID_HOME}/tools
export PATH=${PATH}:${ANDROID_HOME}/platform-tools
```

#### Starting the Android Virtual Device 
``` bash
android avd
```

#### Testing your React Native Installation 
Use the React Native command line interface to generate a new React Native project called "AwesomeProject", then run react-native run-android inside the newly created folder:
``` bash
react-native init AwesomeProject
cd AwesomeProject
react-native run-android
```

#### Modifying your app 
Now that you have successfully run the app, let's modify it.

* Open index.android.js in your text editor of choice and edit some lines.
* Press the R key twice or select Reload from the Developer Menu to see your change!
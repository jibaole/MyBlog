---
title: react native sdk location not found的解决办法
tags:
  - reactnative
categories:
  - Mobile development
toc: true
date: 2016-10-21 10:18:20
description: 
feature:
---

In Android Studio: File ->Project Structure -> Android SDK Location

Copy Android SDK Path enter image description here
![](https://i.stack.imgur.com/MadIg.png)
1. In Your Project_Name -> Android -> Create a File local.properties
2. Open local.properties.
3. Write sdk.dir=/Users/Ashok/Library/Android/sdk
4. Save it.
5. from your project root directory react-native run-android.

参考：http://stackoverflow.com/questions/35141951/react-native-java-lang-runtimeexception-sdk-location-not-found-define-locati
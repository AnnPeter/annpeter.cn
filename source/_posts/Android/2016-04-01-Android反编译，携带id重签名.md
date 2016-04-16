---
layout: post
title: "Android反编译，携带id重签名"
date: 2016-04-01 17:31:32 +1231
comments: true
categories: Android
keywords: Android,Android基础,博客,AnnPeter's Blog
description: "Android反编译，携带id重签名"

---

为了掌握用户的上下线关系，我们可能需要在apk中动态携带信息。
本文主要采用在AndroidManifest.xml文件中的versionName中，添加用户上线的id，达到传递信息的作用。


### 第一步，生成前面文件
> keytool -genkey -v -keystore insurance.keystore -alias insurance-release.keystore -keyalg RSA -validity 10000

<!-- more -->

### 第二步，反编译
> ./apktool d insurance.apk -o insurance

### 第三步，在apktool.yml中修改versionName,如果要动态修改，自己写程序修改这个文件就好啦

### 第四步，重新编译
> ./apktool b insurance -o new.apk

### 第五步，重新签名
> jarsigner  -verbose -keystore insurance.keystore  -storepass insurance -signedjar  insurance\_sign.apk new.apk  insurance-release.keystore
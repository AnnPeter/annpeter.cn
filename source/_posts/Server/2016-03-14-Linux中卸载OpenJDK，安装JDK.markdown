---
layout: post
title: "Linux中卸载OpenJDK，安装JDK"
date: 2016-03-14 22:10:42 +1132
comments: true
categories: Server
keywords: Server,JDK,OpenJDK,Linux,博客,AnnPeter's Blog
description: "Linux中卸载OpenJDK，安装JDK"

---


####首先查询Linux中的OpenJDK。
>rpm -qa | grep java<br/>

####如果有就删除，没有就算了。
>rpm -e --nodeps java-1.8.0-openjdk<br/>
>rpm -e --nodeps javapackages-tools<br/>
>rpm -e --nodeps javapackages-tools<br/>
>rpm -e --nodeps java-1.8.0-openjdk-headless<br/>
>rpm -e --nodeps tzdata-java<br/>
>rpm -e --nodeps python-javapackages<br/>
<!-- more -->

####下载jdk，解压到/usr/local/中
>export JAVA_HOME=/usr/local/jdk1.8.0_73<br/>
>export PATH=$JAVA_HOME/bin:$PATH<br/>
>export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar<br/>

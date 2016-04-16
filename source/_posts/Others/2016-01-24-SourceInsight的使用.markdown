---
layout: post
title: "SourceInsight的使用"
date: 2016-01-24 22:11:23 +0812
comments: true
categories: Others
keywords: SourceInsight,博客,AnnPeter's Blog
description: "SourceInsight的使用"

---

本文主要介绍SourceInsight的简单使用，主要用于查看项目。
<!-- more -->

1. Project->New project新建一个项目，为每个项目设置自己的配置文件，设置项目信息路径
2. Project->Add and Remove Project Files添加项目文件(最好把要分析的源码拷贝至SourceInsight中，以防要编译使用的源码在查看中被修改)，选择文件路径然后Add Tree。
3. Options->Preferences->Display->Trim long path name with ellipses，取消该选项。在标题栏显示文件全路径
4. Project->Default Project Settings，设置Project Source Directory。显示项目文件相对路径。
5. Options->Document Options，File filer添加.s;.S。使项目能添加汇编文件，添加后需重新Add Files。
6. Project->Rebuild Project，重新编译项目，使我们能方便查询到函数定义。
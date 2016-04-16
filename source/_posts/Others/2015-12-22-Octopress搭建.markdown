---
layout: post
title: "Octopress搭建"
date: 2015-12-20 17:31:45 +9723
comments: true
categories: Others
keywords: Octopress,博客,AnnPeter's Blog
description: "Octopress搭建"

---

####安装ruby
```vim
sudo apt-get install ruby
sudo apt-get install ruby-dev

ruby --version #必须显示为1.9.3以上
```

<!-- more -->

####安装Octopress
```vim
$ git clone git://github.com/imathis/octopress.git annpeter.cn

$ cd annpeter.cn
$ gem install bundler
$ bundler install
$ rake install


#常用命令
$ rake new_post["XXX"]	//新建文章，生成的文章位于source/_posts/
$ rake new_page["xxx"]	//新建页面

$ rake install ["theme name"] //设置主题

$ rake generate //生成静态html
$ rake preview	//本地浏览
$ rake deploy	//发布到服务器
```

####安装主题
```vim
$ git clone git://github.com/tommy351/Octopress-Theme-Slash.git .themes/slash
$ rake install['slash']
```


####设置服务器地址，将站点上传
```vim
$ vim Rakefile

#按照自己的实际情况编辑一下信息
ssh_user		= "annpeter@www.itpeter.cn"
ssh_port		= "22"
document_root	= "/www/annpeter.cn"
rsync_delete	= false
```
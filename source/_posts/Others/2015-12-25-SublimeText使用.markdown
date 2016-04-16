---
layout: post
title: "SublimeText使用"
date: 2015-12-25 19:54:32 +6413
comments: true
categories: Others
keywords: SublimeText,博客,AnnPeter's Blog
description: "SublimeText使用"

---

##插件安装准备
#####1、按Ctrl+`调出console
#####2、粘贴以下代码到底部命令行并回车：	
>import urllib2,os;pf='Package Control.sublime-package';ipp=sublime.installed\_packages_path();os.makedirs(ipp) if not os.path.exists(ipp) else None;open(os.path.join(ipp,pf),'wb').write(urllib2.urlopen\('http\://sublime.wbond.net/'+pf.replace(' ','%20')).read())

<!-- more -->

#####3、重启Sublime Text 2。
#####4、如果在Perferences->package settings中看到package control这一项，则安装成功。

###编辑Markdown小技巧
>选中多行，按下Command+Shift+L  ----------   可以同时编辑这些行<br>
>然后使用Command+左右键 ----------   移动光标至行首，行尾<br>
>然后进行多行编辑编辑<br>

##插件
###autofilename
* 功能：快捷输入文件名
* 输入" / "即可看到相对于本项目文件夹的其它文件

###Convert​To​UTF8
* 功能：解决JBK编码文件自动转换为utf-8格式，解决乱码问题

###SublimeLinter
* 代码校验插件，支持多种语言

###Colorcoder
* 高亮所有变量，因此可以极大的简化代码定位。尤其是对那些有阅读障碍的程序员非常有帮助。

###Emmet
* 功能：html标签快捷输入
* 语法


		
	```css
	后代：>
	缩写：nav>ul>li
    <nav>
    	<ul>
        	<li></li>
    	</ul>
	</nav>
	```
	
	```css
	兄弟：+
	缩写：div+p+bq
	<div></div>
    <p></p>
    <blockquote></blockquote>
	```

	```css
	上级：^
	缩写：div+div>p>span+em^bq
	<div></div>
	<div>
	    <p><span></span><em></em></p>
	    <blockquote></blockquote>
	</div>
	```
	
	```css
	分组：(   )
	缩写：div>(header>ul>li*2>a)+footer>p
	<div>
        <header>
            <ul>
                <li><a href=""></a></li>
                <li><a href=""></a></li>
            </ul>
        </header>
        <footer>
            <p></p>
        </footer>
    </div>
    
    
    缩写：(div>dl>(dt+dd)*3)+footer>p
    <div>
        <dl>
            <dt></dt>
            <dd></dd>
            <dt></dt>
            <dd></dd>
            <dt></dt>
            <dd></dd>
        </dl>
    </div>
    <footer>
        <p></p>
    </footer>
	```
	
	```css
	乘法：*
	缩写：ul>li*5
	<ul>
	    <li></li>
	    <li></li>
	    <li></li>
	    <li></li>
	    <li></li>
	</ul>
	```
	
	```css
	自增符号：$
	缩写：ul>li.item$*5
	<ul>
	    <li class="item1"></li>
	    <li class="item2"></li>
	    <li class="item3"></li>
	    <li class="item4"></li>
	    <li class="item5"></li>
	</ul>
	
	缩写：ul>li.item$$$*5
	<ul>
	    <li class="item001"></li>
	    <li class="item002"></li>
	    <li class="item003"></li>
	    <li class="item004"></li>
	    <li class="item005"></li>
	</ul>
	
	缩写：ul>li.item$@-*5
	<ul>
	    <li class="item5"></li>
	    <li class="item4"></li>
	    <li class="item3"></li>
	    <li class="item2"></li>
	    <li class="item1"></li>
	</ul>
	
	缩写：ul>li.item$@3*5
	<ul>
	    <li class="item3"></li>
	    <li class="item4"></li>
	    <li class="item5"></li>
	    <li class="item6"></li>
	    <li class="item7"></li>
	</ul>
	```
	
	```css
	id和class属性
	缩写：form#search.wide
	<form id="search" class="wide"></form>

	缩写：p.class1.class2.class3
	<p class="class1 class2 class3"></p>
	
	自定义属性
	缩写：[a='value1' b="value2"]
	<div a="value1" b="value2"></div>
	```
	
	```css
	缩写：option[value=$]*12>{$月份}
	
	<option value="1">1月份</option>
   	<option value="2">2月份</option>
	<option value="3">3月份</option>
	<option value="4">4月份</option>
	<option value="5">5月份</option>
	<option value="6">6月份</option>
	<option value="7">7月份</option>
	<option value="8">8月份</option>
	<option value="9">9月份</option>
	<option value="10">10月份</option>
	<option value="11">11月份</option>
	<option value="12">12月份</option>
	```
	
	```css
	文本：{}
	缩写：a{Click me}
	<a href="">Click me</a>
	
	缩写：p>{Click }+a{here}+{ to continue}
	<p>Click <a href="">here</a> to continue</p>
	```
	
	```css
	缩写：script:src
	<script src=""></script>
	```
    
###更多Emmet语法
![更多Emmet语法](/upload/2015/OCT/12/imgs/1444639540.png)

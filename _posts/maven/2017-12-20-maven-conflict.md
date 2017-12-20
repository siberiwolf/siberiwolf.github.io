---
layout: post
title:  "使用intellij idea解决maven依赖冲突"
date:   2017-12-20 21:00:00
categories: maven
---

使用maven管理项目的时候，会遇到项目依赖A，项目依赖B，则maven会导入A依赖的所有类包，B依赖的所有类包，则问题来了  

Q:如果A和B同时依赖于X，假设A依赖于X的1.0版本，而B依赖于X的2.0版本，则项目究竟依赖于X的1.0还是2.0版本呢？  
A:看Classloader的加载顺序了，假设Classloader先加载X_1.0，而它就不会再加载X_2.0了，如果A恰恰希望使用X_2.0呢，冏啊

Q:如何使用idea解决这个问题(Community Edition貌似没有这个功能)  
A:第一步，查看项目整体的依赖树，双击打开项目的POM文件，点击右键选择Diagrams->show dependencies，选择1:1显示，apply current layout  
  第二步，查找到有多个依赖的类包，选择不需要的版本，右键选择exclude  
  第三步，右键POM.xml，选择maven->reimport  

Q:如何用mvn命令查看依赖树  
A:mvn -s E:\settings.xml dependency:tree

Q:把依赖树信息导入文件  
A:mvn -s E:\settings.xml dependency:tree -Doutput=E:\tree.txt
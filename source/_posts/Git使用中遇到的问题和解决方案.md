title: Git使用中遇到的问题和解决方案
date: 2015-11-21 16:45:13
tags: 
- Git 
- 问题与解决方案
---
1.git不能add，不能commit子文件夹
 - 删除隐藏的.git文件夹后重新git init
 - [参考链接](http://www.v2ex.com/t/69360)
2.git向github上传项目的基本步骤
 - 进入要上传的目录
 - git init
 - git add.
 - git commit -m "message"
 - git remote add origin SSH
 - git push -u origin master
3."fatal:remote origin already exists."
 - git remote rm origin
 - [参考链接](http://blog.csdn.net/chaihuasong/article/details/37911723)

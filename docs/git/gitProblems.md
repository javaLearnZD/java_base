## git在使用过程中碰到的问题

### 1、git的.gitignore文件不起作用？

#### 问题？
在项目的根目录中放了.gitignore文件，但是在运行git status的时候，始终能看到需要忽略的文件

#### 原因
git的.gitignore文件只能忽略原来没有被track的文件，如果某些文件已经被纳入版本管理中，那么无论我们怎么修改.gitignore都是无效的

#### 解决方法
用rm命令清除相关的缓存就行了，这样文件就会以未追踪的形式出现，然后重新提交就好了

``` 
git rm -rf --cache .
 
git add .

git commit -m "update .gitignore"

```
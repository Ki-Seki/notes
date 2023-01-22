title:      Git 学习笔记
author:     小 K
summary:    原来这里只是介绍 gitignore 的语法规则，现在改为总结各种 Git 知识点。
datetime:   2021-08-23 16:44
            2022-04-23 22:17
tags:       Git
            gitignore
privacy:    1

[TOC]

## 提交代码至远程仓库

强制提交：`git push -u origin master -f`，该代码会用本地代码强制覆盖远程仓库。

## 回滚代码

1. 使用 `git log` 确定要回滚到的 commit 的 id；
2. 使用 `git reset --hard commit_id`
3. 使用 `git push origin`，推送到远程分支。

回退到上个版本的快捷命令是 `git reset --hard HEAD^`. `HEAD^` 是上个版本，`HEAD^^` 是上上个版本。

## gitignore 语法规则

### 规则优先级

在 .gitingore 文件中，每一行指定一个忽略规则，Git 检查忽略规则的时候有多个来源，它的优先级如下（由高到低）：

1. 从命令行中读取可用的忽略规则
2. 当前目录定义的规则
3. 父级目录定义的规则，依次递推
4. $GIT_DIR/info/exclude 文件中定义的规则
5. core.excludesfile中定义的全局规则

### 匹配语法

* 空格不匹配任意文件，可作为分隔符，可用反斜杠转义
* 开头的文件标识注释，可以使用反斜杠进行转义
* ! 开头的模式标识否定，该文件将会再次被包含，如果排除了该文件的父级目录，则使用 ! 也不会再次被包含。可以使用反斜杠进行转义
* / 结束的模式只匹配文件夹以及在该文件夹路径下的内容，但是不匹配该文件
* / 开始的模式匹配项目跟目录
* 如果一个模式不包含斜杠，则它匹配相对于当前 .gitignore 文件路径的内容，如果该模式不在 .gitignore 文件中，则相对于项目根目录
* ** 匹配多级目录，可在开始，中间，结束
* ? 通用匹配单个字符
* [] 通用匹配单个字符列表

### 匹配举例

* `bin/`: 忽略当前路径下的bin文件夹，该文件夹下的所有内容都会被忽略，不忽略 bin 文件
* `/bin`: 忽略根目录下的bin文件
* `/*.c`: 忽略 cat.c，不忽略 build/cat.c
* `debug/*.obj`: 忽略 debug/io.obj，不忽略 debug/common/io.obj 和 tools/debug/io.obj
* `**/foo`: 忽略/foo, a/foo, a/b/foo等
* `a/**/b`: 忽略a/b, a/x/b, a/x/y/b等
* `!/bin/run.sh`: 不忽略 bin 目录下的 run.sh 文件
* `*.log`: 忽略所有 .log 文件
* `config.php`: 忽略当前路径的 config.php 文件

### .gitignore 规则不生效?

.gitignore只能忽略那些原来没有被track的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的。

解决方法就是先把本地缓存删除（改变成未track状态），然后再提交:

```shell
$ git rm -r --cached .
$ git add .
$ git commit -m 'update .gitignore'
```

### 文件被 .gitignore 忽略了?

```shell
$ git add App.class
The following paths are ignored by one of your .gitignore files:
App.class
Use -f if you really want to add them.
```

找到那个规则：

```shell
$ git check-ignore -v App.class
.gitignore:3:*.class    App.class
```

## 错误：fatal: unable to access 'https://github.com/...'

github.com 一般来说是无法直接访问的，必须要走代理。根据代理软件的不同，访问 github.com 的方法也稍有不同。

使用 SSTap 时，开启全局就可以访问；使用 Clash 时，开启全局仅是浏览器访问网站的全局，而非所有软件的全局。

让 git 将代码同步给 github 的方法：

```cmd
查看全局配置项中的代理项
git config --global -l

配置代理
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890

取消代理
git config --global --unset http.proxy 
git config --global --unset https.proxy 
```
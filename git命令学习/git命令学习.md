# git命令学习

[Git 教程 | 菜鸟教程 (runoob.com)](https://www.runoob.com/git/git-tutorial.html)

## github上传仓库的流程：

**第一次操作**

1. 在本地文件夹下打开git命令行，输入git init，初始化本地仓库
2. 修改自己的文件，代码/md等文件
3. 本地文件夹传递到暂存区：`git add .`将文件夹中的所有文件都推送到缓冲区，也可以单独推送某个文件，例如`git add README.md`
4. 将暂存区内容写入本地仓库中：`git commit -m 提交修改的信息`
5. 创建远程仓库：`git remote add 仓库名称 仓库url`，只需要在第一次做
6. 本地仓库上传到远程仓库：`git push (origin) (main)`，()的意思是需要更改，`origin`是创建远程仓库时的仓库名称，默认是这个，`main`是选择提交的分支

**第n次操作**

1. 修改自己文件夹，代码/md文件
2. 本地文件夹传递到暂存区：`git add .`
3. 将暂存区内容写入本地仓库中：`git commit -m 提交修改的信息`
4. 本地仓库上传到远程仓库：`git push (origin) (main)`



## git branch

1.查看当前分支，当前所在的分支会有一个前缀 `*`，例如：

```
* main
  develop
  feature/branch-name
```

2.创建新的分支

```
git branch new_branch
```

3.删除分支

```
git branch -D new_branch
```



## git checkout

1.切换分支，在` git branch`之后，显示的分支中，设置想要切换的分支，上个例子中

```
git checkout develop
```

2.创建并切换新的分支，和branch命令合二为一，-b的意思就是先执行branch命令

```
git checkout -b new_branch
```

3.切换版本tags，例如拉去下来px4的源码，需要切换到版本v1.13.2

```
git checkout V1.13.2
```



## git describe

查看当前项目版本号：

```
git describe --tags
```



## git add

将本地文件夹上传到暂存区

```
git add . 或者git add 文件名.后缀
```

删除暂存区的文件

`--cached`是只删除暂存区，而不删除本地文件；如果不加这个参数，会删除本地文件

```
git rm --cached 文件.后缀
```

```
git rm -r --cached 文件夹
```



## git ls-files

查看暂存区的文件

```
git ls-files
```



## git commit

缓冲区提交到本地仓库中，ubuntu下是单引号，

```
git commit -m "windows修改的说明"  / 'linux修改的说明'
```



## git tag

本地创建新的tag版本

```
git tag v1.0
```

推送到远程仓库

```
git push origin --tag
```



## 远程仓库

删除分支

```
git push origin --delete new_branch
```

合并分支：进入主分支，输入待合并的分支

```
git merge new_branch
```



## 本地仓库

```
git branch -D new_branch
```


# Git

待读：[IDEA中Git的使用详解 - 如何下笔呢 - 博客园 (cnblogs.com)](https://www.cnblogs.com/niudaben/p/12017898.html)

## Git配置

### 配置文件

git配置文件路径：

1. 系统配置：git安装目录\etc\gitconfig
2. 用户配置：~\\.gitconfig

### 查看配置

```shell
# 查看所有配置
git config -l
# 查看当前仓库的配置
git config --local -l
# 查看全局配置
git config --global -l
# 查看系统配置
git config --system -l
```

### 配置用户名和邮箱

```shell
# 全局配置
git config --global user.name "joby"
git config --global user.email "11111@qq.com"
# 为仓库单独配置，cd到仓库目录下
git config user.name "joby"
git config user.email "11111@qq.com"
```



### 配置ssh

git config命令用法如下：

![git-config-usage](img/git-config-usage-16376601567584.png)



## Git基础命令及用法

### 本地仓库和远程仓库之间的交互

git clone

git push

git pull

### 本地仓库与暂存区之间的交互

git commit

git checkout

### 暂存区和工作区之间的交互

git add

git rm

git restore

### 日志

git log 使用q退出

[Git 各指令的本质，真是通俗易懂啊 (qq.com)](https://mp.weixin.qq.com/s/_Klu56D9pN_slPidhytd4g)

![](img/git-terminal.png)

## Git文件状态流转

### 文件的四种状态

- 未追踪（untracked）：工作目录中新建文件的状态，使用`git add [file]`将文件放入暂存区，文件状态变为已暂存；如果要撤销使用`git rm --cached [file]`；使用`git commit -m`提交暂存区变化到本地仓库；
- 未修改（unmodified）：未修改的文件和本地仓库文件一致，编辑后变为已修改状态，使用git add [file]变为已暂存；取消暂存可以使用git checkout --[]
- 已修改（modified）：同上
- 已暂存（staged）：提交文件到本地仓库，文件变为未修改状态；撤销暂存`git restore --staged <file>...`



### 撤销对工作区文件的修改（撤销编辑和add）

#### git restore

`git restore --staged <file>...`从暂存区移除新文件，文件状态变为untracked

`git restore <file>...`丢弃对工作目录下文件的修改，（对执行过git add的文件无效）

#### git checkout

git checkout <file> 丢弃工作区修改（恢复还没有执行git add的文件）



### 撤销对暂存区文件的修改（撤销commit）

`git reset --soft HEAD^`：撤销一次commit，保留暂存区（暂存区恢复到commit之前的状态）

`git reset --hard HEAD^`：撤销一次commit，不保留暂存区（暂存区恢复到add之前的状态）

`git reset HEAD <file>` ：仅改变暂存区，并不改变工作区文件

`git reset --hard <commit编号>`：对commit编号后的提交都有影响

`git revert <commit编号>`：使用一次新的commit抵消指定编号的commit所作的修改，对commit编号后的提交无影响



reset本地仓库不会保留提交，revert本地仓库会保留提交记录；

git相对引用

- 使用^表示一次提交
- 使用~2表示两次提交

### 撤销push

git log

git reset --hard <commit编号>

git push origin master --force







![状态流转](img/u=2640741206,444576451&fm=173&app=49&f=JPEG.jpeg)







## Git分支（branch）

分支（branch）是一个指针，指向某次提交（commit），创建新分支就是创建一个指针，创建分支不会造成多余的内存开销；head指针（当前分支）指向某个分支，head指针等于当前分支指针；

### 分支切换

分支切换就是改变head指针

#### checkout

![image-20220621154328783](img/image-20220621154328783.png)

#### reset



#### rebase



![](img/git-checkout.png)

## Git使用问题

### 恢复文件

- git checkout : 可以恢复还没有执行git add的文件
- git reset : 把文件从git的staged区域移除（取消git add），再使用git checkout进行恢复
- git reset --hard : 恢复整个仓库为当前分支的最新版本

## Git使用规范

### 分支规范

### 提交注释规范

[别乱提交代码了，看下大厂 Git 提交规范是怎么做的！ (qq.com)](https://mp.weixin.qq.com/s/39NTALGqA6FHk34ikSw0Yw)

[求求你了！别瞎提交代码了，看人家 Git 提交规范那叫一个舒服！ (qq.com)](https://mp.weixin.qq.com/s/VUzOwjLAhHcTEbA-EnQemQ)

## 使用gitee同步笔记

Git 全局设置:

```shell
git config --global user.name "joby"
git config --global user.email "bithup@foxmail.com"
# 如果全局配置有冲突，可以不使用 --global参数
```

创建 git 仓库:

```shell
mkdir java-notes
cd java-notes
git init
touch README.md
git add README.md
git commit -m "first commit"
git remote add origin https://gitee.com/bithup/java-notes.git
git push -u origin master
```

已有仓库?

```shell
cd existing_git_repo
git remote add origin https://gitee.com/bithup/java-notes.git
git push -u origin master
```

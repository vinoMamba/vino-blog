# GIT

> git 主要解决版本控制的问题，让你的项目版本

## 命令行的基础操作

```shell
pwd   # 查看当前目录的绝对路径
ls    # 查看当前目录的内容
mkdir # 创建文件夹
touch # 创建文件
cp    # 复制文件
rm -rf  #慎用!! 
```

## git 六行配置

```shell
# 配置用户名
git config --global user.name 你的英文名

# 配置邮箱
git config --global user.email 你的邮箱

# 配置git的默认行为，默认是Matching,此行为会push所有分支，simple只会push当前分支
git config --global push.default simple

# 解决提交文件名如果是中文名会出现乱码的问题 
git config --global core.quotepath false

# 配置commit的时候以哪种编辑器打开填写commit信息
git config --global core.editor "code --wait"

# 这一个配置是解决不同系统提交代码换行的问题,具体不太了解，windows平台下配就完了
git config --global core.autocrlf input
```

s## git 最常用的命令

#### 1. git init 初始化git 仓库，用来方项目不同的版本

```shell
cd ~/Desktop

mkdir git-demo

cd git-demo

git init 
```

#### 2. git status

> 查看状态

#### 3. git add

> 选择哪些变动是需要提交的，路径可以是绝对路径或者相对路径，或者 `.` 表示提交所有的

```shell
git add index.html

git add .
```

#### 4. .gitignore 文件

> 用来描述哪些文件变动是不需要提交的。常见的有 `node_modules`,`.idea`,`.vscode`

```shell
touch .gitignore

vim .gitignore
```

#### 5. git commit

> 提交，并说明提交理由，如果提交的内容有空格，就需要用引号括起来
>
> 有两种形式：
>   1. git commit -m
>   2. git commit -v 会弹出编辑器，回顾都做了哪些改动，迫使自己将提交理由写的更详细

#### 6. git log

> 查看提交信息，可以看到所有的提交记录，以及版本号，能方便回到某个版本

```shell
λ git log
commit 30a6dd16d233f1e4f37f5f0f9289d8ae6e19ef1b (HEAD -> master)
Author: vino <vino0908@outlook.com>
Date:   Thu Jul 29 23:52:53 2021 +0800

    third commit

commit a80db53baf628c45a590db66e569096a1bc6e0af
Author: vino <vino0908@outlook.com>
Date:   Thu Jul 29 23:52:16 2021 +0800

    second commit

commit 1505e9e483f721791c510cf6f77f1e024239e3a6
Author: vino <vino0908@outlook.com>
Date:   Thu Jul 29 23:50:34 2021 +0800

    git init commit
```

#### 7. git reset --hard  xxxxxx

> xxxxxx 是提交号的前六位，其实只有确保不会和其他版本号重复即可
> 在 reset 之前确保自己的代码都已经commit了，不然没有commit的代码会消失

#### 8. git reflog

> git log 只会显示之前的提交记录
>
> git reflog 会显示所有的提交记录

## git分支

```shell
# 查看分支
git branch

# 创建分支
git branch x

# 切换分支
git checkout x
```

> git checkout 用于切换分支，当前目录有未提交的代码，只要跟另一个分支不冲突就不予理会

#### git merge x

> 用于合并分支,将另一个分支合并到当前分支
>
> 合并分支有可能会出现冲突，也可能不会出现冲突，出现了冲突解决即可

#### 解决冲突的步骤

1. 先合并分支
2. git status 查看冲突的文件，并打开此文件
3. 全局搜索四个等于号 `======`
4. 在上下两个部分选择需要保留的代码
5. 删除无用的代码包括 `<<<<` , `====`
6. git add
7. git status -sb 解决下一个冲突
8. git commit (不需要添加提交信息)

#### git stash 通灵术

```shell
git stash 

git stash list

git stash pop

git stash clear

```

## 其他高级配置

> 可以设置快捷命令，具体操作如下
>
> 在home目录创建一个.bashrc文件
> 在文件里面添加下面的对应的代码
> 不成功在命令行运行 source ~/.bashrc

```shell
touch ~/.bashrc       //命令行创建文件的操作
alias ga="git add"
alias gst="git status"
alias gc="git commit -v"
alias gm="git commit -m"
alias gp="git push"
alias gco="git checkout"
alias gco="git checkout"
```












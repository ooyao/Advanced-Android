# Git 是什么

Git是一个很流行、很好用的分布式的版本控制系统，使用Git也算是程序员的必备技能了。



虽然Git与其他流行的版本控制工具用起来比较类似，但在对信息的存储和认知方式上却有很大差异，所以在学习Git之前最好理清你对其他版本管理系统已有的认知，避免在学习Git的过程中发生混淆。



## Git 中文件的三种状态

在 Git 中文件可能处于已修改（modified）、已提交（committed)或已暂存（staged）三种状态之一：

- ***已修改（modified）***

已修改表示修改了文件，但还没有保存到数据库中。

- ***已提交（committed)***

已提交表示数据已经安全地保存在本地数据库中。

- ***已暂存（staged）***

已暂存表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中。



基本的 Git 工作流程如下：

1. 在工作区中修改文件。
2. 将你想要下次提交的更改选择性地暂存，这样只会将更改的部分添加到暂存区。
3. 提交更新，找到暂存区的文件，将快照永久存储到 Git 目录。



如果 Git 目录中保存着特定版本的文件，就属于`已提交`状态。如果文件已修改并放入暂存区，就属于`已暂存`状态。如果自上次检出后，作了修改但还没有放到暂存区域，就是`已修改`状态。



# 配置 Git

在安装 Git 之后，可以对 Git 进行配置，每台计算机上只需要配置一次，程序升级时会保留配置信息，并且随时可以修改。

Git 自带一个` git config`的工具来帮助设置控制 Git 外观和行为的配置变量。这些变量存储在三个不同的位置：

1. `/etc/gitconfig`文件：包含系统上每一个用户及他们仓库的通用配置。如果在执行`git config`时带上`--system`选项，那么它就会读写该文件中的配置变量。（由于它是系统配置文件，因此你需要管理员或者超级用户权限来修改它。此目录默认在`/usr/local/git/etc/gitconfig`目录下，及 git 安装目录。）
2. `~/.gitconfig`或`~/.config/git/config`文件：只针对当前用户。你可以传递`--global`选项让 Git 读写此文件，这会对你系统上所有的仓库生效。
3. 当前使用仓库的 Git 目录中的`config`文件（即`.git/config`）：针对该仓库。你可以传递`--local`选项让 Git 强制读写此文件，虽然默认情况下用的就是它。（在执行命令前须先定位到 Git 仓库所在目录才能生效。）



每一个级别都会覆盖上一级别的配置，所以`.git/config`的配置变量会覆盖`/etc/gitconfig/`中的配置变量。



可以通过以下命令查看所有的配置以及它们所在的文件：

`$ git config --list --show-origin`



## 配置用户信息

安装完 Git 之后，第一件事情就是设置你的用户名和邮箱地址。这一点很重要，因为每一个 Git 提交都会使用这些信息，它们会写入到你的每一次提交中，不可更改：

`$ git config --global user.name "ooyao"`

`$ git config --global user.email "ooyao1024@gmail.com"`



如果使用了`--global`选项则指定的是全局配置，所有项目默认都会使用这个配置，如果想针对特定项目使用不同的用户名称与邮件地址时，可以在该项目目录下运行没有`--global`选项的命令配置。（默认选项是`--local`。）



## 配置文本编辑器

如果未配置默认 Git 会使用操作系统默认的文本编辑器。

如果你想要使用不同的文本编辑器，例如 Emacs，可以这样做：

`$ git config --global core.editor emacs`



在 Windows 系统上，需要制定可执行文件的完整路径。



## 检查配置信息

如果想要检查你的配置，可以使用`git config --list`命令来列出所有 Git 当时能找到的配置。你可能会看到重复的变量名，因为 Git 会从不同的文件中读取同一个配置（例如：`/etc/gitconfig`与`~/.gitconfig`)。



可以通过`git config <key>`:来检查 Git 的某一项配置。

```shell
$ git config user.name
ooyao
```



由于 Git 会从多个文件中读取同一配置变量的不同值，因此你可能会在其中看到医疗之外的值，此时你可以查询 Git 中该变量的原始值，它会告诉你哪一个配置文件最后设置了该值：

```shell
 $ git config --show-origin rerere.autoUpdate
file:/home/johndoe/.gitconfig   false
```



## 获取帮助

若你使用 GIt 时需要获取帮助，有三种等价的方法可以找到 Git 命令的综合手册（man page）：

`$ git help <verb>
 $ git <verb> --help
 $ man git-<verb>`



例如，要获得`git config`命令的手册，执行如下命令：

```shell
$ git help config
```



如果不需要全面的手册，只需要可用选项的快速参考，那么可以用`-h`选项获得更简明的“help”输出：

```shell
$ git add -h
usage: git add [<options>] [--] <pathspec>...

    -n, --dry-run         dry run
    -v, --verbose         be verbose

    -i, --interactive     interactive picking
    -p, --patch           select hunks interactively
    -e, --edit            edit current diff and apply
    -f, --force           allow adding otherwise ignored files
    -u, --update          update tracked files
    --renormalize         renormalize EOL of tracked files (implies -u)
    -N, --intent-to-add   record only the fact that the path will be added later
    -A, --all             add changes from all tracked and untracked files
    --ignore-removal      ignore paths removed in the working tree (same as --no-all)
    --refresh             don't add, only refresh the index
    --ignore-errors       just skip files which cannot be added because of errors
    --ignore-missing      check if - even missing - files are ignored in dry run
    --chmod (+|-)x        override the executable bit of the listed files
```



# Git基础

## 获取 Git 仓库

通常有两种获取 GIt 仓库的方式：

### 将尚未进行版本控制的本地目录转换为 Git 仓库

如果你有一个尚未进行版本控制的项目目录，想要用 Git 进行管理，那么首先需要进入该项目目录中。例如在 Mac 中：

```shell
$ cd /Users/user/my_project
```

之后执行：

```
$ git init
```

该命令将创建一个名为`.git`的子目录，这个子目录含有你初始化的 Git 仓库中所有的必须文件，这些文件是 Git 仓库的骨干。但是，在这个时候，我们仅仅是做了一个初始化的操作，你的项目里的文件还没有被跟踪。



如果在一个已存在文件的文件夹中进行版本控制，需要追踪这些文件并进行初始化提交。可以通过`git add`命令来指定所需的文件来进行追踪，然后执行`git commit`：

```sh
$ git add *.c
$ git add LICENSE
$ git commit -m 'initial project version'
```



### 从其它服务器克隆一个已存在的 Git 仓库

使用`git clone`命令可以克隆一个已经存在的 Git 仓库。Git 克隆的是该 Git 仓库服务器上的所有数据。当你执行`git clone`命令的时候，默认配置下远程Git 仓库中的每一个文件的每一个版本都将被拉取下来。

如果服务器的磁盘坏掉了，可以使用任何一个克隆下来的用户端来重建服务器上的仓库。这就是 Git 分布式的优势。

克隆仓库的命令是`git clone <url>`。比如，要克隆 Git 的链接库`libgit2`，困用下面的命令。

```shell
$ git clone https://github.com/libgit2/libgit2
```

这会在当前目录下创建一个名为“libgit2”的目录，并在这个目录下初始化一个`.git`文件夹，从远程仓库拉取下所有数据放入`.git`文件夹，然后从中读取最新版本的文件的拷贝。如果你进入到这个新建的`libgit2`文件夹，你会发现所有的项目文件已经在里面了，准备就绪等待后续的开发和使用。



可以在克隆远程仓库的时候自定义本地仓库的名字，你困通过额外的参数指定新的目录名：

```shell
$ git clone https://github.com/libgit2/libgit2 mylibgit
```

此时目标目录名辩位了`mulibgit`。

Git 支持多种数据传输协议。 上面的例子使用的是` https:// `协议，不过你也可以使用 `git://` 协议或者使用 `SSH `传输协议，比如 `user@server:path/to/repo.git` 。



## 记录每次更新

## 检查当前文件状态

用`git status`命令查看哪些文件处于什么状态。如果在克隆仓库后立即使用此命令，会看到类似这样的输出：

```shell
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
```

从上面的信息可知当前的分支是`master`，`master`是默认的分支名。并且当前分支与远程分支没有偏离，也没有未提交的信息。因为克隆成功之后我们并未修改任何文件。



在项目下创建一个新的`README`文件。然后使用`git status`命令：

```shell
$ echo 'My Project' > README
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Untracked files:
(use "git add <file>..." to include in what will be committed)
README
nothing added to commit but untracked files present (use "git add" to
track)
```










































































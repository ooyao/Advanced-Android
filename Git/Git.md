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

```
$ git config user.name
ooyao
```



由于 Git 会从多个文件中读取同一配置变量的不同值，因此你可能会在其中看到医疗之外的值，此时你可以查询 Git 中该变量的原始值，它会告诉你哪一个配置文件最后设置了该值：

```
 $ git config --show-origin rerere.autoUpdate
file:/home/johndoe/.gitconfig   false
```



## 获取帮助

若你使用 GIt 时需要获取帮助，有三种等价的方法可以找到 Git 命令的综合手册（man page）：

`$ git help <verb>
 $ git <verb> --help
 $ man git-<verb>`



例如，要获得`git config`命令的手册，执行如下命令：

```
$ git help config
```



如果不需要全面的手册，只需要可用选项的快速参考，那么可以用`-h`选项获得更简明的“help”输出：

```
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







# Git命令

- git --version 查看 git 版本信息
- 










































































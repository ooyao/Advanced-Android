***此内容为[Pro Git](https://git-scm.com/book/en/v2) 笔记。***



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

## git status 检查当前文件状态

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



在状态报告中可以看到新建的`README`文件出现在`Untracked files`下面。这表示 Git 还未将其纳入跟踪范围，如果想要跟踪管理则可以使用`git add`命令。



## git add 跟踪新文件

使用命令`git add`开始跟踪一个文件。所以，要跟踪`README`文件，运行：

```shell
$ git add README
```



此时再运行`git status`命令，会看到`README`文件已被跟踪，并处于暂存状态：

```shell
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
(use "git restore --staged <file>..." to unstage)
new file:   README
```



只要在`Changes to be committed`这行下面，就说明是暂存状态。如果此时提交，那么该文件爱你在你运行 `git add`时的版本将被留存在后续的历史记录中。`git init命令使用文件或目录的路径作为参数；如果参数是目录的路径，该命令将递归跟踪该目录下的所有文件。



## 忽略文件

一般我们总会有些文件无需纳入 Git 的管理，例如日志文件、或者编译过程中创建的临时文件等。这种情况下可以创建一个名为`.gitignore`的文件，列出要忽略的文件的模式。例如：



```
$ cat .gitignore
*.[oa]
*~
```



第一行告诉 Git 忽略所有以 .o 或 .a 结尾的文件。一般这类对象文件和存档文件都是编译过程中出现的。 第二 行告诉 Git 忽略所有名字以波浪符(~)结尾的文件，许多文本编辑软件(比如 Emacs)都用这样的文件名保存 副本。 此外，你可能还需要忽略 log，tmp 或者 pid 目录，以及自动生成的文档等等。 要养成一开始就为你的 新仓库设置好 .gitignore 文件的习惯，以免将来误提交这类无用的文件。

文件 .gitignore 的格式规范如下:

• 所有空行或者以 # 开头的行都会被 Git 忽略。
 • 可以使用标准的 glob 模式匹配，它会递归地应用在整个工作区中。
 • 匹配模式可以以(/)开头防止递归。
 • 匹配模式可以以(/)结尾指定目录。
 • 要忽略指定模式以外的文件或目录，可以在模式前加上叹号(!)取反。

所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。 星号(*)匹配零个或多个任意字符;[abc] 匹配 任何一个列在方括号中的字符 (这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c); 问号(?)只 匹配一个任意字符;如果在方括号中使用短划线分隔两个字符， 表示所有在这两个字符范围内的都可以匹配 (比如 [0-9] 表示匹配所有 0 到 9 的数字)。 使用两个星号(**)表示匹配任意中间目录，比如 a/**/z 可以 匹配 a/z 、 a/b/z 或 a/b/c/z 等。



我们再看一个 .gitignore 文件的例子:



```.gitignore
# 忽略所有的 .a 文件 
*.a
# 但跟踪所有的 lib.a，即便你在前面忽略了 .a 文件 
!lib.a
# 只忽略当前目录下的 TODO 文件，而不忽略 subdir/TODO 
/TODO
# 忽略任何目录下名为 build 的文件夹 
build/
# 忽略 doc/notes.txt，但不忽略 doc/server/arch.txt 
doc/*.txt
# 忽略 doc/ 目录及其所有子目录下的 .pdf 文件 
doc/**/*.pdf
```



GitHub 有一个十分详细的针对数十种项目及语言的 .gitignore 文件列表， 你可以在 https://github.com/github/gitignore 找到它。

在最简单的情况下，一个仓库可能只根目录下有一个 .gitignore 文件，它递归地应用到整 个仓库中。 然而，子目录下也可以有额外的 .gitignore 文件。子目录中的 .gitignore 文件中的规则只作用于它所在的目录中。 



## 查看已暂存和未暂存的修改

使用`git diff`命令可以查看当前做的哪些更新尚未暂存，有哪些更新已暂存并准备好下次提交。`git diff` 能通过文件补丁的格式更加具体地显示哪些行发生了改变。



要查看尚未暂存的文件更新了哪些部分，不加参数直接输入`git diff`:

```
$ git diff
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 8ebb991..643e24f 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -65,7 +65,8 @@ branch directly, things can get messy.
Please include a nice description of your changes when you submit your
PR;
if we have to read the whole diff to figure out why you're contributing
in the first place, you're less likely to get feedback and have your
change
-merged in.
+merged in. Also, split your changes into comprehensive chunks if your
patch is
+longer than a dozen lines.
If you are starting to work on a particular area, feel free to submit a
PR
that highlights your work in progress (and note in the PR title that it's
```



此命令比较的是工作目录中当前文件和暂存区域快照之间的差异。 也就是修改之后还没有暂存起来的变化内 容。

若要查看已暂存的将要添加到下次提交里的内容，可以用 `git diff --staged` 命令。 这条命令将比对已暂存 文件与最后一次提交的文件差异:

```
$ git diff --staged
diff --git a/README b/README
new file mode 100644
index 0000000..03902a1
--- /dev/null
+++ b/README
@@ -0,0 +1 @@
+My Project
```

请注意，`git diff` 本身只显示尚未暂存的改动，而不是自上次提交以来所做的所有改动。 所以有时候你一下子暂 存了所有更新过的文件，运行`git diff`后却什么也没有，就是这个原因。

像之前说的，暂存`CONTRIBUTING.md`后再编辑，可以使用`git status`查看已被暂存的修改或未被暂存的修 改。 如果我们的环境(终端输出)看起来如下:

```
$ git add CONTRIBUTING.md
$ echo '# test line' >> CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
(use "git reset HEAD <file>..." to unstage)
modified:   CONTRIBUTING.md
Changes not staged for commit:
(use "git add <file>..." to update what will be committed)
(use "git checkout -- <file>..." to discard changes in working
directory)
modified:   CONTRIBUTING.md
```

现在运行`git diff`看暂存前后的变化:

```
$ git diff
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 643e24f..87f08c8 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -119,3 +119,4 @@ at the
## Starter Projects
See our [projects
list](https://github.com/libgit2/libgit2/blob/development/PROJECTS.md).
+# test line
```



然后用`git diff --cached`查看已经暂存起来的变化(`--staged`和`--cached`是同义词):

```
$ git diff --cached
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 8ebb991..643e24f 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -65,7 +65,8 @@ branch directly, things can get messy.
Please include a nice description of your changes when you submit your
PR;
if we have to read the whole diff to figure out why you're contributing
in the first place, you're less likely to get feedback and have your
change
-merged in.
+merged in. Also, split your changes into comprehensive chunks if your
patch is
+longer than a dozen lines.
If you are starting to work on a particular area, feel free to submit a
PR
that highlights your work in progress (and note in the PR title that it's
```



## 提交更新



在准备提交之前先使用`git add`命令将已修改或新建的文件添加到暂存区，也可以使用 `git status` 进行检查，然后运行`git commit`命令进行提交。

```shell
$ git commit
```



这样会启动你选择的文本编辑器来输入提交的说明。



启动的编辑器是通过 `Shell` 的环境变量`EDITOR` 指定的，一般为 `vim` 或 `emacs`。 可以使用 `git config --global core.editor` 命令设置你喜欢的编辑器。



编辑器会显示类似下面的文本信息(本例选用 Vim 的屏显方式展示):

```shell

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# Your branch is up-to-date with 'origin/master'.
#
# Changes to be committed:
# new file:
#   modified:
#
~
README
CONTRIBUTING.md
~
~
".git/COMMIT_EDITMSG" 9L, 283C
```



可以看到，默认的提交信息包含最后一次运行`git status`的输出，放在注释行里，另外开头还有一个空行， 供你输入提交说明。 你完全可以去掉这些注释行，不过留着也没关

系，多少能帮你回想起这次更新的内容有哪 些。

更详细的内容修改提示可以用 -v 选项查看，这会将你所作的更改的 diff 输出呈现在编辑器 中，以便让你知道本次提交具体作出哪些修改。

退出编辑器时，Git 会丢弃注释行，用你输入的提交说明生成一次提交。

另外，你也可以在 commit 命令后添加 -m 选项，将提交信息与命令放在同一行，如下所示:

```shell
$ git commit -m "Story 182: Fix benchmarks for speed"
[master 463dc4f] Story 182: Fix benchmarks for speed
2 files changed, 2 insertions(+)
create mode 100644 README
```

可以看到，提交后它会告诉你，当前是在哪个分支(master)提交的，本 次提交的完整 SHA-1 校验和是什么(463dc4f)，以及在本次提交中，有多少文件修订过，多少行添加和删改 过。



## 跳过使用暂存区

尽管使用暂存区域的方式可以精心准备要提交的细节，但有时候这么做略显繁琐。 Git 提供了一个跳过使用暂 存区域的方式， 只要在提交的时候，给 `git commit `加上 `-a` 选项，Git 就会自动把所有已经跟踪过的文件暂存 起来一并提交，从而跳过`git add`步骤:

```shell
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
(use "git add <file>..." to update what will be committed)
(use "git checkout -- <file>..." to discard changes in working
directory)
modified:   CONTRIBUTING.md
no changes added to commit (use "git add" and/or "git commit -a")
$ git commit -a -m 'added new benchmarks'
[master 83e38c7] added new benchmarks
1 file changed, 5 insertions(+), 0 deletions(-)
```

提交之前不再需要 `git add` 文件“CONTRIBUTING.md”了。 这是因为 `-a` 选项使本次提交包含了 所有修改过的文件。 这很方便，但是要小心，有时这个选项会将不需要的文件添加到提交中,并且如果是新建的文件则不会被提交。



## 移除文件

要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除(确切地说，是从暂存区域移除)，然后提交。 可以用`git rm`命令完成此项工作，并连带从工作目录中删除指定的文件，这样以后就不会出现在未跟踪文件清 单中了。

如果只是简单地从工作目录中手工删除文件，运行 `git status` 时就会在 “Changes not staged for commit” 部分(也就是 未暂存清单)看到:

```shell
$ rm PROJECTS.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
(use "git add/rm <file>..." to update what will be committed)
(use "git checkout -- <file>..." to discard changes in working
directory)
deleted:    PROJECTS.md
no changes added to commit (use "git add" and/or "git commit -a")
```

然后再运行`git rm`记录此次移除文件的操作:

```sh
$ git rm PROJECTS.md
rm 'PROJECTS.md'
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
(use "git reset HEAD <file>..." to unstage)
deleted:    PROJECTS.md
```

下一次提交时，该文件就不再纳入版本管理了。 如果要删除之前修改过或已经放到暂存区的文件，则必须使用 强制删除选项 `-f`(译注:即 `force` 的首字母)。 这是一种安全特性，用于防止误删尚未添加到快照的数据，这 样的数据不能被 Git 恢复。

另外一种情况是，我们想把文件从 Git 仓库中删除(亦即从暂存区域移除)，但仍然希望保留在当前工作目录 中。 换句话说，你想让文件保留在磁盘，但是并不想让 Git 继续跟踪。 当你忘记添加 .gitignore 文件，不小 心把一个很大的日志文件或一堆 .a 这样的编译生成文件添加到暂存区时，这一做法尤其有用。 为达到这一目 的，使用 `--cached` 选项:

```shell
$ git rm --cached README
```



git rm命令后面可以列出文件或者目录的名字，也可以使用glob模式。比如:

```shell
$ git rm log/\*.log
```



注意到星号 `*` 之前的反斜杠` \`， 因为 Git 有它自己的文件模式扩展匹配方式，所以我们不用 `shell` 来帮忙展开。 此命令删除 `log/` 目录下扩展名为` .log` 的所有文件。 类似的比如:

```shell
$ git rm \*~
```

该命令会删除所有名字以 ~ 结尾的文件。



## 移动文件

不像其它的 VCS 系统，Git 并不显式跟踪文件移动操作。 如果在 Git 中重命名了某个文件，仓库中存储的元数 据并不会体现出这是一次改名操作。 不过 Git 非常聪明，它会推断出究竟发生了什么，至于具体是如何做到的， 我们稍后再谈。

既然如此，当你看到 Git 的 `mv` 命令时一定会困惑不已。 要在 Git 中对文件改名，可以这么做:

```shell
$ git mv file_from file_to
```



它会恰如预期般正常工作。 实际上，即便此时查看状态信息，也会明白无误地看到关于重命名操作的说明



```shell
$ git mv README.md README
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
(use "git reset HEAD <file>..." to unstage)
renamed:    README.md -> README
```



其实，运行git mv就相当于运行了下面三条命令:



```shell
$ mv README.md README
$ git rm README.md
$ git add README
```



如此分开操作，Git 也会意识到这是一次重命名，所以不管何种方式结果都一样。 两者唯一的区别是，`mv` 是一 条命令而非三条命令，直接用 `git mv` 方便得多。 不过有时候用其他工具批处理重命名的话，要记得在提交前 删除旧的文件名，再添加新的文件名。



## 查看提交历史

使用`git log`可以回顾提交历史。在项目中运行`git log`命令时，可以看到类似下面的输出：

```
$ git log
commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

	changed the version number

commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Sat Mar 15 16:40:33 2008 -0700

	removed unnecessary test

commit a11bef06a3f659402fe7563abf99ad00de2209e6
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Sat Mar 15 10:31:28 2008 -0700
	
	first commit
```



`git log --pretty=format` 常用的选项:

| 选项 | 说明                                        |
| ---- | ------------------------------------------- |
| %H   | 提交的完整哈希值                            |
| %h   | 提交的简写哈希值                            |
| %T   | 树的完整哈希值                              |
| %t   | 树的简写哈希值                              |
|      | 父提交的完整哈希值                          |
| %p   | 父提交的简写哈希值                          |
| %an  | 作者名字                                    |
| %ae  | 作者的电子邮件地址                          |
| %ad  | 作者修订日期(可以用 --date=选项 来定制格式) |
| %ar  | 作者修订日期，按多久以前的方式显示          |
| %cn  | 提交者的名字                                |
| %ce  | 提交者的电子邮件地址                        |
| %cd  | 提交日期                                    |
| %cr  | 提交日期(距今多长时间)                      |
| %s   | 提交说明                                    |



当 `oneline` 或 `format` 与另一个 `log` 选项 `--graph` 结合使用时尤其有用。 这个选项添加了一些 `ASCII` 字符串 来形象地展示你的分支、合并历史:



```
  $ git log --pretty=format:"%h %s" --graph
  * 2d3acf9 ignore errors from SIGCHLD on trap
  *  5e3ee11 Merge branch 'master' of git://github.com/dustin/grit
  |\
  | * 420eac9 Added a method for getting the current branch.
  * | 30e367c timeout code and tests
  * | 5a09431 add timeout protection to grit
  * | e1193f8 support for heads with slashes in them
  |/
  * d6016bc require time for xmlschema
  *  11d191e Merge branch 'defunkt' into local
```



`git log` 的常用选项:



| 选项            | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| -p              | 按补丁格式显示每个提交引入的差异。                           |
| --stat          | 显示每次提交的文件修改统计信息。                             |
| --shortstat     | 只显示 --stat 中最后的行数修改添加移除统计。                 |
| --name-only     | 仅在提交信息后显示已修改的文件清单。                         |
| --name-status   | 显示新增、修改、删除的文件清单。                             |
| --abbrev-commit | 仅显示 SHA-1 校验和所有 40 个字符中的前几个字符。            |
| --relative-date | 使用较短的相对时间而不是完整格式显示日期(比如“2 weeks ago”)。 |
| --graph         | 在日志旁以 ASCII 图形显示分支与合并历史。                    |
| --pretty        | 使用其他格式显示历史提交信息。可用的选项包括 oneline、short、full、fuller 和 format(用来定义自己的格式)。 |
| --oneline       | --pretty=oneline --abbrev-commit合用的简写。                 |



### 限制输出长度

类似 `--since` 和 `--until` 这种按照时间作限制的选项很有用。 例如，下面的命令会列出最近两周的所 有提交:

```
$ git log --since=2.weeks
```

该命令可用的格式十分丰富——可以是类似"2008-01-15"的具体的某一天，也可以是类似"2 years 1 day 3 minutes ago"的相对日期。



还可以过滤出匹配指定条件的提交。 用 `--author` 选项显示指定作者的提交，用 `--grep` 选项搜索提交说明中 的关键字



限制 `git log` 输出的选项



| 选项              | 说明                                       |
| ----------------- | ------------------------------------------ |
| `-<n>`            | 仅显示最近的 n 条提交                      |
| --since, --after  | 仅显示指定时间之后的提交。                 |
| --until, --before | 仅显示指定时间之前的提交。                 |
| --author          | 仅显示作者匹配指定字符串的提交。           |
| --committer       | 仅显示提交者匹配指定字符串的提交。         |
| --grep            | 仅显示提交说明中包含指定字符串的提交。     |
| -S                | 仅显示添加或删除内容匹配指定字符串的提交。 |



隐藏合并提交：

按照你代码仓库的工作流程，记录中可能有为数不少的合并提交，它们所包含的信息通常并不 多。 为了避免显示的合并提交弄乱历史记录，可以为 `log` 加上 `--no-merges` 选项。



### 撤销操作

有时候我们提交完了才发现漏掉了几个文件没有添加，或者提交信息写错了。 此时，可以运行带有 `--amend` 选 项的提交命令来重新提交:

```
$ git commit --amend
```



这个命令会将暂存区中的文件提交。 如果自上次提交以来你还未做任何修改(例如，在上次提交后马上执行了 此命令)， 那么快照会保持不变，而你所修改的只是提交信息。

文本编辑器启动后，可以看到之前的提交信息。 编辑后保存会覆盖原来的提交信息。 

例如，你提交后发现忘记了暂存某些需要的修改，可以像下面这样操作:

```
 $ git commit -m 'initial commit'
 $ git add forgotten_file
 $ git commit --amend
```

最终你只会有一个提交——第二次提交将代替第一次提交的结果。



修补提交最明显的价值是可以稍微改进你最后的提交，而不会让“啊，忘了添加一个文件”或 者 “小修补，修正笔误”这种提交信息弄乱你的仓库历史。



### 取消暂存的文件

使用 `git reset HEAD <file>` 来取消暂存。 可以这样来取消暂存 CONTRIBUTING.md 文件:

```
  $ git reset HEAD CONTRIBUTING.md
  Unstaged changes after reset:
  M   CONTRIBUTING.md
  $ git status
  On branch master
  Changes to be committed:
    (use "git reset HEAD <file>..." to unstage)
      renamed:    README.md -> README
  Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working
  directory)
      modified:   CONTRIBUTING.md
```



### 撤销对文件的修改

请务必记得`git checkout -- <file>`是一个危险的命令。你对那个文件在本地的任何修 改都会消失——Git 会用最近提交的版本覆盖掉它。 除非你确实清楚不想要对那个文件的本地 修改了，否则请不要使用这个命令

```
  $ git checkout -- CONTRIBUTING.md
  $ git status
  On branch master
  Changes to be committed:
    (use "git reset HEAD <file>..." to unstage)
      renamed:    README.md -> README
```



可以看到那些修改已经被撤消了。



## 远程仓库的使用

远程仓库未必表示仓库在网络或者互联网上的某个位置，也可以是在本地的主机上。只是通常情况下是设置到网络上，这样可以方便多人协作工作。



### 查看仓库的使用

运行 `git remote` 命令查看远程仓库名字。

```
  $ git clone https://github.com/schacon/ticgit
  Cloning into 'ticgit'...
  remote: Reusing existing pack: 1857, done.
  remote: Total 1857 (delta 0), reused 0 (delta 0)
  Receiving objects: 100% (1857/1857), 374.35 KiB | 268.00 KiB/s, done.
  Resolving deltas: 100% (772/772), done.
  Checking connectivity... done.
  $ cd ticgit
  $ git remote
  origin
```



你也可以指定选项 `-v`，会显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL。



```
  $ git remote -v
  origin  https://github.com/schacon/ticgit (fetch)
  origin  https://github.com/schacon/ticgit (push)
```



如果你的远程仓库不止一个，该命令会将它们全部列出。 例如，与几个协作者合作的，拥有多个远程仓库的仓 库看起来像下面这样:



```
$ cd grit
$ git remote -v
bakkdoor  https://github.com/bakkdoor/grit (fetch)
bakkdoor  https://github.com/bakkdoor/grit (push)
cho45	  https://github.com/cho45/grit (fetch)
cho45     https://github.com/cho45/grit (push)
defunkt   https://github.com/defunkt/grit (fetch)
defunkt   https://github.com/defunkt/grit (push)
koke      git://github.com/koke/grit.git (fetch)
koke      git://github.com/koke/grit.git (push)
origin    git@github.com:mojombo/grit.git (fetch)
origin    git@github.com:mojombo/grit.git (push)
```



这表示我们能非常方便地拉取其它用户的贡献。我们还可以拥有向他们推送的权限。



### 添加远程仓库

运行 `git remote add <shortname> <url>` 添加一个新的远程 Git 仓库，同时指定一个方便 使用的简写:



```
  $ git remote
  origin
  $ git remote add pb https://github.com/paulboone/ticgit
  $ git remote -v
  origin  https://github.com/schacon/ticgit (fetch)
  origin  https://github.com/schacon/ticgit (push)
  pb  https://github.com/paulboone/ticgit (fetch)
  pb  https://github.com/paulboone/ticgit (push)
```

现在你可以在命令行中使用字符串 `pb` 来代替整个` URL`。 例如，如果你想拉取` Paul` 的仓库中有但你没有的信 息，可以运行`git fetch pb`:

```
  $ git fetch pb
  remote: Counting objects: 43, done.
  remote: Compressing objects: 100% (36/36), done.
  remote: Total 43 (delta 10), reused 31 (delta 5)
  Unpacking objects: 100% (43/43), done.
  From https://github.com/paulboone/ticgit
   * [new branch]      master     -> pb/master
   * [new branch]      ticgit     -> pb/ticgit
```



现在 `Paul` 的 `master` 分支可以在本地通过 `pb/master` 访问到——你可以将它合并到自己的某个分支中， 或者如果你想要查看它的话，可以检出一个指向该点的本地分支。



### 从远程仓库中抓取与拉取

从远程仓库中获得数据，可以执行:

```
$ git fetch <remote>
```



这个命令会访问远程仓库，从中拉取所有你还没有的数据。 执行完成后，你将会拥有那个远程仓库中所有分支 的引用，可以随时合并或查看。



如果你使用 `clone` 命令克隆了一个仓库，命令会自动将其添加为远程仓库并默认以 `“origin”` 为简写。 所 以，`git fetch origin`会抓取克隆(或上一次抓取)后新推送的所有工作。必须注意`git fetch`命令只会 将数据下载到你的本地仓库——它并不会自动合并或修改你当前的工作。所以如果发生冲突需要手动合并。



如果你的当前分支设置了跟踪远程分支， 那么可以用 `git pull` 命令 来自动抓取后合并该远程分支到当前分支。 这或许是个更加简单舒服的工作流程。默认情况下，`git clone` 命 令会自动设置本地 `master` 分支跟踪克隆的远程仓库的 `master` 分支(或其它名字的默认分支)。 运行 `git pull` 通常会从最初克隆的服务器上抓取数据并自动尝试合并到当前所在的分支。



### 推送到远程仓库

使用`git push <remote> <branch>`。可以将项目推送到服务器上。例如将 `master` 分支推送到 `origin` 服务器：

```
$ git push origin master
```



只有当你有所克隆服务器的写入权限，并且之前没有人推送过时，这条命令才能正常运行。如果服务器上存在其他人的推送并且你没有将其拉取到本地时是不能`push`的。此时需先使用`git pull`将其他人推送的内容拉取到本地并合并之后在进行推送。



### 查看某个远程仓库

如果想要查看某一个远程仓库的更多信息，可以使用 `git remote show <remote>` 命令。 如果想以一个特 定的缩写名运行这个命令，例如 `origin`，会得到像下面类似的信息:



```
  $ git remote show origin
  * remote origin
    Fetch URL: https://github.com/schacon/ticgit
    Push  URL: https://github.com/schacon/ticgit
    HEAD branch: master
    Remote branches:
      master                               tracked
      dev-branch                           tracked
    Local branch configured for 'git pull':
      master merges with remote master
    Local ref configured for 'git push':
      master pushes to master (up to date)
```



它同样会列出远程仓库的 `URL` 与跟踪分支的信息。 这些信息非常有用，它告诉你正处于 `master` 分支，并且如 果运行 `git pull`， 就会抓取所有的远程引用，然后将远程 `master` 分支合并到本地 `master` 分支。 它也会列 出拉取到的所有远程引用。



### 远程仓库的重命名与移除

运行 `git remote rename` 来修改一个远程仓库的简写名。 例如，想要将 `pb` 重命名为 `paul`，可以用 `git remote rename`这样做:

```
  $ git remote rename pb paul
  $ git remote
  origin
  paul
```



值得注意的是这同样也会修改你所有远程跟踪的分支名字。 那些过去引用 `pb/master` 的现在会引用 `paul/master`。

如果因为一些原因想要移除一个远程仓库——你已经从服务器上搬走了或不再想使用某一个特定的镜像了， 又或 者某一个贡献者不再贡献了——可以使用`git remote remove`或`git remote rm`:

```
  $ git remote remove paul
  $ git remote
  origin
```

一旦你使用这种方式删除了一个远程仓库，那么所有和这个远程仓库相关的远程跟踪分支以及配置信息也会一起 被删除。



## tags














































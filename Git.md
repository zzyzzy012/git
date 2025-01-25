# Git

## 1.Git简介

**版本控制**

集中化的版本控制系统（Centralized Version Control Systems，简称 CVCS）：CVS、Subversion 以及 Perforce 等，都有一个单一的集中管理的服务器，保存所有文件的修订版本，而协同工作的人们都通过客户端连到这台服务器，取出最新的文件或者提交更新。 

分布式版本控制系统（Distributed Version Control System，简称 DVCS）：把代码仓库完整地镜像下来，包括完整的历史记录。

Git 用以计算校验和的机制叫做 SHA-1 散列（hash，哈希）。 这是一个由 40 个十六进制字符（0-9 和 a-f）组成的字符串，基于 Git 中文件的内容或目录结构计算出来。Git 数据库中保存的信息都是以文件内容的哈希值来索引，而不是文件名。

```
24b9da6552252987aa493b52f8696cd6d3b00373
```

**三种状态：** **已提交（committed）**、**已修改（modified）** 和 **已暂存（staged）**。

- 已修改表示修改了文件，但还没保存到数据库中。
- 已暂存表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中。
- 已提交表示数据已经安全地保存在本地数据库中。

**三个阶段：工作区、暂存区以及 Git 目录。**

- 工作区是对项目的某个版本独立提取出来的内容。 这些从 Git 仓库的压缩数据库中提取出来的文件，放在磁盘上供你使用或修改。
- 暂存区是一个文件，保存了下次将要提交的文件列表信息，一般在 Git 仓库目录中。 按照 Git 的术语叫做“索引”，不过一般说法还是叫“暂存区”。
- Git 仓库目录是 Git 用来保存项目的元数据和对象数据库的地方。 这是 Git 中最重要的部分，从其它计算机克隆仓库时，复制的就是这里的数据。

![](D:\学习\program\前端学习\Git\git_imgs\areas.png)

查看配置项。

```
git config --list
```

获取帮助， `-h` 选项获得更简明的 `help`

```
git help <verb>
git help config
git add -h | git -h add
```

## 2.Git基础

### 2.1 获取git仓库

如果你有一个尚未进行版本控制的项目目录，想要用 Git 来控制它，那么首先需要进入该项目目录中。

该命令将创建一个名为 `.git` 的子目录，这个子目录含有你初始化的 Git 仓库中所有的必须文件，这些文件是 Git 仓库的骨干。

```
cd /c/user/my_project
git init
```

如果在一个已存在文件的文件夹（而非空文件夹）中进行版本控制，你应该开始追踪这些文件并进行初始提交。 可以通过 `git add` 命令来指定所需的文件来进行追踪，然后执行 `git commit` ：

```
$ git add *.c
$ git add LICENSE
$ git commit -m 'initial project version'
```

用的命令是"clone"而不是"checkout"。 这是 Git 区别于其它版本控制系统的一个重要特性，Git 克隆的是该 Git 仓库服务器上的几乎所有数据，而不是仅仅复制完成你的工作所需要文件。

克隆仓库的命令是`git clone <url> `

如果你想在克隆远程仓库的时候，自定义本地仓库的名字，你可以通过额外的参数指定新的目录名：

```
$ git clone https://github.com/libgit2/libgit2
$ git clone https://github.com/libgit2/libgit2 mylibgit
```

### 2.2 记录每次更新到仓库

git仓库中的文件状态：**已跟踪** 或 **未跟踪**

![](D:\学习\program\前端学习\Git\git_imgs\lifecycle.png)

检查当前文件状态： `git status` 查看哪些文件处于什么状态

跟踪新文件：`git add` 命令使用文件或目录的路径作为参数；如果参数是目录的路径，该命令将递归地跟踪该目录下的所有文件。

可以用它**跟踪新文件，暂存已修改的文件，或者把已跟踪的文件放到暂存区，还能用于合并时把有冲突的文件标记为已解决状态等。** 将这个命令理解为**“精确地将内容添加到下一次提交中”**而不是“将一个文件添加到项目中”要更加合适。

```bash
$ git add src
$ git add user.txt
$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   src/index.js
        new file:   user.txt
```

状态简览：`git status -s` 命令或 `git status --short` 命令。

**新添加的未跟踪文件前面有 `??` 标记，新添加到暂存区中的文件前面有 `A` 标记，修改过的文件前面有 `M` 标记。 输出中有两栏，左栏指明了暂存区的状态，右栏指明了工作区的状态。**

```
$ git status -s
 M README
MM Rakefile
A  lib/git.rb
M  lib/simplegit.rb
?? LICENSE.txt
```

 `README` 文件在工作区已修改但尚未暂存，而 `lib/simplegit.rb` 文件已修改且已暂存。 `Rakefile` 文件已修改，暂存后又作了修改，因此该文件的修改中既有已暂存的部分，又有未暂存的部分。

**忽略文件**

创建一个名为 `.gitignore` 的文件，列出要忽略的文件的模式。

第一行告诉 Git 忽略所有以 `.o` 或 `.a` 结尾的文件。一般这类对象文件和存档文件都是编译过程中出现的。 第二行告诉 Git 忽略所有名字以波浪符（~）结尾的文件，许多文本编辑软件都用这样的文件名保存副本。

```
$ cat .gitignore
*.[oa]
*~
```

所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。 星号（`*`）匹配零个或多个任意字符；`[abc]` 匹配任何一个列在方括号中的字符 （这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）； 问号（`?`）只匹配一个任意字符；如果在方括号中使用短划线分隔两个字符， 表示所有在这两个字符范围内的都可以匹配（比如 `[0-9]` 表示匹配所有 0 到 9 的数字）。 使用两个星号（`**`）表示匹配任意中间目录，比如 `a/**/z` 可以匹配 `a/z` 、 `a/b/z` 或 `a/b/c/z` 等。

**查看已暂存和未暂存的修改**

`git diff`比较的是工作目录中当前文件和暂存区域快照之间的差异。 也就是修改之后还没有暂存起来的变化内容。`git diff`本身只显示尚未暂存的改动，而不是自上次提交以来所做的所有改动。 所以有时候你一下子暂存了所有更新过的文件，运行 `git diff` 后却什么也没有，就是这个原因。

`git diff --staged` 比对已暂存文件与最后一次提交的文件差异。

`git diff --cached` 查看已经暂存起来的变化（ `--staged` 和 `--cached` 是同义词）

```
$ git diff
```

**提交更新**

```
$ git commit
$ git commit -m message(提交的信息注释)
```

**跳过使用暂存区域**

`git commit -a` Git 会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 `git add` 步骤

```
git commit -a -m 'added new benchmarks'
```

**移除文件**

要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除（确切地说，是从暂存区域移除），然后提交。 可以用 `git rm` 命令完成此项工作，并连带从工作目录中删除指定的文件，这样以后就不会出现在未跟踪文件清单中了。

`git rm` 命令后面可以列出文件或者目录的名字，也可以使用 `glob` 模式。

 如果要删除之前修改过或已经放到暂存区的文件，则必须使用强制删除选项 `-f`。

把文件从 Git 仓库中删除（亦即从暂存区域移除），但仍然希望保留在当前工作目录中。 换句话说，你想让文件保留在磁盘，但是并不想让 Git 继续跟踪。 当你忘记添加 `.gitignore` 文件，不小心把一个很大的日志文件或一堆 `.a` 这样的编译生成文件添加到暂存区时，这一做法尤其有用。

```
$ git rm --cached README
```

### 2.3 查看提交历史

`git log` 会按时间先后顺序列出所有的提交，最近的更新排在最上面。

```
git log
```

 `-p` 或 `--patch` ，它会显示每次提交所引入的差异（按 **补丁** 的格式输出）。 你也可以限制显示的日志条目数量，例如使用 `-2` 选项来只显示最近的两次提交。

```
git log -p -2
```

想看到每次提交的简略统计信息，可以使用 `--stat` 选项。

```
git log --stat
```

`--pretty`可以使用不同于默认格式的方式展示提交历史。 这个选项有一些内建的子选项供你使用。

`oneline` 会将每个提交放在一行显示，在浏览大量的提交时非常有用。

`short`，`full` 和 `fuller` 选项，它们展示信息的格式基本一致，但是详尽程度不一。

| 选项              | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| `-p`              | 按补丁格式显示每个提交引入的差异。                           |
| `--stat`          | 显示每次提交的文件修改统计信息。                             |
| `--shortstat`     | 只显示 --stat 中最后的行数修改添加移除统计。                 |
| `--name-only`     | 仅在提交信息后显示已修改的文件清单。                         |
| `--name-status`   | 显示新增、修改、删除的文件清单。                             |
| `--abbrev-commit` | 仅显示 SHA-1 校验和所有 40 个字符中的前几个字符。            |
| `--relative-date` | 使用较短的相对时间而不是完整格式显示日期（比如“2 weeks ago”）。 |
| `--graph`         | 在日志旁以 ASCII 图形显示分支与合并历史。                    |
| `--pretty`        | 使用其他格式显示历史提交信息。可用的选项包括 oneline、short、full、fuller 和 format（用来定义自己的格式）。 |
| `--oneline`       | `--pretty=oneline --abbrev-commit` 合用的简写。              |

### 2.4 撤销操作

取消暂存的文件

> 你已经修改了两个文件并且想要将它们作为两次独立的修改提交， 但是却意外地输入 `git add *` 暂存了它们两个。只取消暂存两个中的一个：`git reset HEAD <file>`

```
git reset HEAD <file>...
$ git reset HEAD CONTRIBUTING.md
```

撤消对文件的修改

```
git checkout -- <file>...
git checkout -- CONTRIBUTING.md
```

撤销在工作区文件的修改

```
git restore <file>...
```

### 2.5 远程仓库的使用

**查看远程仓库**

`git remote` 会列出你指定的每一个远程服务器的简写。 如果你已经克隆了自己的仓库，那么至少应该能看到 origin ——这是 Git 给你克隆的仓库服务器的默认名字。

指定选项 `-v`，会显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL。

```bash
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

$ git remote -v
origin	https://github.com/schacon/ticgit (fetch)
origin	https://github.com/schacon/ticgit (push)
```

**添加远程仓库**

`git remote add <shortname> <url>` 添加一个新的远程 Git 仓库，同时指定一个方便使用的简写。

```bash
$ git remote
origin
$ git remote add pb https://github.com/paulboone/ticgit
$ git remote -v
origin	https://github.com/schacon/ticgit (fetch)
origin	https://github.com/schacon/ticgit (push)
pb	https://github.com/paulboone/ticgit (fetch)
pb	https://github.com/paulboone/ticgit (push)
```

**从远程仓库中抓取与拉取：**`git fetch <remote>`

```
使用字符串 pb 来代替整个 URL
$ git fetch pb
```

如果你的当前分支设置了跟踪远程分支， 那么可以用 `git pull` 命令来自动抓取后合并该远程分支到当前分支。 默认情况下，`git clone` 命令会自动设置本地 master 分支跟踪克隆的远程仓库的 `master` 分支（或其它名字的默认分支）。 运行 `git pull` 通常会从最初克隆的服务器上抓取数据并自动尝试合并到当前所在的分支。

**推送到远程仓库：**`git push <remote> <branch>`

```
$ git push origin master
```

**查看某个远程仓库：**`git remote show <remote>`

```
$ git remote show origin
```

**远程仓库的重命名与移除**

`git remote rename a b`将远程仓库a重命名为b。

```
$ git remote rename pb paul
$ git remote
origin
paul
```

`git remote remove a`删除远程仓库a。

```
$ git remote remove paul
$ git remote
origin
```

### 2.6 打标签

**列出标签：**`git tag`（可带上可选的 `-l` 选项 `--list`，按照通配符列出标签需要 `-l` 或 `--list` 选项）

```
$ git tag
user
```

**创建标签**

附注标签：存储在 Git 数据库中的一个完整对象， 它们是可以被校验的，其中包含打标签者的名字、电子邮件地址、日期时间， 此外还有一个标签信息，并且可以使用 GNU Privacy Guard （GPG）签名并验证。 

`-m` 选项指定了一条将会存储在标签中的信息。

```bash
$ git tag -a v1.4 -m "my version 1.4"
$ git tag
v0.1
v1.3
v1.4
```

`git show` 命令可以看到标签信息和与之对应的提交信息。

```bash
$ git show v1.4
tag v1.4
Tagger: Ben Straub <ben@straub.cc>
Date:   Sat May 3 20:19:12 2014 -0700

my version 1.4

commit ca82a6dff817ec66f44342007202690a93763949
Author: Scott Chacon <schacon@gee-mail.com>
Date:   Mon Mar 17 21:52:11 2008 -0700

    changed the version number
```

轻量标签：本质上是将提交校验和存储到一个文件中——没有保存任何其他信息。 

创建轻量标签，只需要提供标签名字`git tag <tagName>`。

```bash
$ git tag v1.4-lw
$ git tag
v0.1
v1.3
v1.4
v1.4-lw
v1.5
```

**后期打标签：**通过`$ git log --pretty=oneline`查找到要打标签的文件，`git tag -a <tagName> <fileCode>`

```
$ git tag -a v1.2 9fceb02
```

**共享标签：**默认情况下，`git push` 命令并不会传送标签到远程仓库服务器上。 在创建完标签后你必须显式地推送标签到共享服务器上。 这个过程就像共享远程分支一样——你可以运行 `git push origin <tagname>`。

`git push origin --tags`把所有不在远程仓库服务器上的标签全部传送到那里。

**删除标签：**`git tag -d <tagname>`

> 注意上述命令并不会从任何远程仓库中移除这个标签，你必须用 `git push <remote> :refs/tags/<tagname>` 或者`git push origin --delete <tagname>`来更新你的远程仓库。

```bash
$ git tag -d v1.4-lw
Deleted tag 'v1.4-lw' (was e7d5add)

$ git push origin :refs/tags/v1.4-lw
To /git@github.com:schacon/simplegit.git
 - [deleted]         v1.4-lw
$ git push origin --delete <tagname>
```

### 2.7 Git创建别名

`git config --global alias.别名 命令`

```
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
```

为了解决取消暂存文件的易用性问题，可以向 Git 中添加你自己的取消暂存别名：

```
$ git config --global alias.unstage 'reset HEAD --'
下面两个命令等价
$ git unstage fileA
$ git reset HEAD -- fileA
```

## 3.Git分支

`git branch`得到分支列表。`*`表示`HEAD`指向的那个分支(也就是所在分支)。

```
$ git branch
  master
* pay
  test
```

`git branch 分支名`创建分支。

`git branch -d 分支`删除分支。

```
$ git branch testing
```

`git checkout 分支名`切换分支进入该分支。

```
$ git checkout testing
```

`git checkout -b 分支名`创建分支并进入该分支。

```bash
$ git checkout -b iss53
是下面两行的简写
$ git branch iss53
$ git checkout iss53
```

`git branch -d 分支`删除分支，没合并分支里的文件无法删除，可以使用`git branch -D 分支`强制删除分支。

```
$ git branch -d hotfix
```

`git merge 分支名`合并分支。

```
将iss53分支下的文件合并入master
$ git checkout master
Switched to branch 'master'
$ git merge iss53
Merge made by the 'recursive' strategy.
index.html |    1 +
1 file changed, 1 insertion(+)
```

解决冲突合并的问题：`git merge 分支名`，同名文件冲突，手动解决，重新添加`git add 文件名`，重新提交`git commit -m '提交信息'`。

`git branch --merged`查看已合并的分支，这时可以使用`git branch -d 已合并的分支名`删除已合并的分支。

`git branch --no-merged`查看未合并的分支。

**变基：**`git rebase`类似将绳子拉直，树干不同树枝接到主枝上。

```
将主题分支 （即本例中的 server）变基到目标分支（即 master）上。
$ git rebase master server
```

### Git远程分支

`git push <remote> <branch>`将本地分支推送到远程仓库。

**跟踪分支**：从一个远程跟踪分支检出一个本地分支会自动创建所谓的“跟踪分支”（它跟踪的分支叫做“上游分支”）。 跟踪分支是与远程分支有直接关系的本地分支。

`git checkout -b <branch> <remote>/<branch>`快捷操作`git checkout --track <remote>/<branch>`

```bash
$ git checkout -b serverfix origin/serverfix
Branch serverfix set up to track remote branch serverfix from origin.
Switched to a new branch 'serverfix'

$ git checkout --track origin/serverfix
Branch serverfix set up to track remote branch serverfix from origin.
Switched to a new branch 'serverfix'
```

`git branch --set-upstream-to <remote>/<branch>`设置已有的本地分支跟踪一个刚刚拉取下来的远程分支，或者想要修改正在跟踪的上游分支。简写形式`git branch -u <remote>/<branch>`。

```
$ git branch -u origin/serverfix
Branch serverfix set up to track remote branch serverfix from origin.
```

`git branch -vv`查看设置的所有跟踪分支，会列出详细信息。

```bash
$ git branch -vv
  iss53     7e424c3 [origin/iss53: ahead 2] forgot the brackets
  master    1ae2a45 [origin/master] deploying index fix
* serverfix f8674d9 [teamone/server-fix-good: ahead 3, behind 1] this should do it
  testing   5ea463a trying something new
```

这些数字的值来自于你从每个服务器上最后一次抓取的数据。 这个命令并没有连接服务器，它只会告诉你关于本地缓存的服务器数据。 如果想要统计最新的领先与落后数字，需要在运行此命令前抓取所有的远程仓库。 

```
$ git fetch --all; git branch -vv
```

**拉取:**当 `git fetch` 命令从服务器上抓取本地没有的数据时，它并不会修改工作目录中的内容。 它只会获取数据然后让你自己合并。 然而，有一个命令叫作 `git pull` 在大多数情况下它的含义是一个 `git fetch` 紧接着一个 `git merge` 命令。 如果有一个像之前章节中演示的设置好的跟踪分支，不管它是显式地设置还是通过 `clone` 或 `checkout` 命令为你创建的，`git pull` 都会查找当前分支所跟踪的服务器与分支， 从服务器上抓取数据然后尝试合并入那个远程分支。

由于 `git pull` 的魔法经常令人困惑所以通常单独显式地使用 `fetch` 与 `merge` 命令会更好一些。

**删除远程分支：** `git push <remote> --delete <branch>` ， 这个命令做的只是从服务器上移除这个指针。

```bash
从服务器上删除 serverfix 分支:
$ git push origin --delete serverfix
To https://github.com/schacon/simplegit
 - [deleted]         serverfix
```

## 3.Git内部工作原理

git工作流：分布式工作流

![](D:\学习\program\前端学习\Git\git_imgs\Snipaste_2025-01-25_15-08-51.jpg)

![](D:\学习\program\前端学习\Git\git_imgs\workflow2.jpg)

![](D:\学习\program\前端学习\Git\git_imgs\workflow3.jpg)

`git init`创建的.git文件夹下的默认文件。

对于一个全新的 `git init` 版本库，这将是你看到的默认结构。 `description` 文件仅供 GitWeb 程序使用，我们无需关心。 `config` 文件包含项目特有的配置选项。 `info` 目录包含一个全局性排除（global exclude）文件， 用以放置那些不希望被记录在 `.gitignore` 文件中的忽略模式（ignored patterns）。 `hooks` 目录包含客户端或服务端的钩子脚本（hook scripts）。

剩下的四个条目很重要：`HEAD` 文件、（尚待创建的）`index` 文件，和 `objects` 目录、`refs` 目录。 它们都是 Git 的核心组成部分。 `objects` 目录存储所有数据内容；`refs` 目录存储指向数据（分支、远程仓库和标签等）的提交对象的指针； `HEAD` 文件指向目前被检出的分支；`index` 文件保存暂存区信息。

### 3.1 objects

通过底层命令 `git hash-object` 来演示上述效果——该命令可将任意数据保存于 `.git/objects` 目录（即 **对象数据库**），并返回指向该数据对象的唯一的键。

```bash
初始化一个新的 Git 版本库，并确认 objects 目录为空，下面命令创建了 pack 和 info 子目录，但均为空。
$ git init test
Initialized empty Git repository in /tmp/test/.git/
$ cd test
$ find .git/objects
.git/objects
.git/objects/info
.git/objects/pack
$ find .git/objects -type f
```

用 `git hash-object` 创建一个新的数据对象并将它手动存入你的新 Git 数据库中。`git hash-object` 会接受你传给它的东西，而它只会返回可以存储在 Git 仓库中的唯一键。 `-w` 选项会指示该命令不要只返回键，还要将该对象写入数据库中。 最后，`--stdin` 选项则指示该命令从标准输入读取内容；若不指定此选项，则须在命令尾部给出待存储文件的路径。

```
$ echo 'test content' | git hash-object -w --stdin
d670460b4b4aece5915caf5c68d12f560a9fe3e4
```

Git 存储内容的方式——一个文件对应一条内容， 以该内容加上特定头部信息一起的 SHA-1 校验和为文件命名。 校验和的前两个字符用于命名子目录，余下的 38 个字符则用作文件名。

`cat-file` 命令从 Git 那里取回数据，`cat-file -p` 选项可指示该命令自动判断内容的类型，并为我们显示大致的内容。

```
$ git cat-file -p d670460b4b4aece5915caf5c68d12f560a9fe3e4
test content
```
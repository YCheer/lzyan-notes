> 以下为观看 [廖雪峰Git教程](https://www.liaoxuefeng.com/wiki/896043488029600) 笔记
>
> 更多内容：[Git飞行规则(Flight Rules)](https://github.com/k88hudson/git-flight-rules/blob/master/README_zh-CN.md)、[动画git教程](https://learngitbranching.js.org/?demo=&locale=zh_CN)、[An emoji guide for your commit messages](https://gitmoji.dev/)、[实际项目中如何使用Git做分支管理](https://blog.csdn.net/ShuSheng0007/article/details/80791849)、[Git的奇技淫巧](https://github.com/521xueweihan/git-tips)

## 基本操作

### 操作介绍

打开 `git bash` 在本地创建一个版本仓库，就是在合适的地方创建一个空目录，然后切入到此目录

```
mkdir learngit
cd learngit
```

把这个目录变成 `git` 可以管理的仓库，并且此时 `git` 会告诉你这是一个空的仓库(empty git repository)，同时还会生成 `.git` 这个目录，这个目录是 `git` 用来版本追踪管理本库用的，所以不可以乱动。但是当你 `ls` 查看当前的目录的时候，你会看不到有此目录的存在的，因为已经隐藏了，需要用 `ls -ah`

```
git init
Initialized empty Git repository in C:/Users/lzy/Desktop/learngit/.git/
```

然后在仓库下面随意编写一个文件随意内容，`readme.txt` 内容如下：

```
Git is a version control system.
Git is free software.
```

告诉 `Git`，把文件添加到仓库，执行上面的命令没有任何显示，这就对了，Unix 的哲学是**“没有消息就是好消息”**，说明添加成功。

```
git add readme.txt
```

告诉 `Git`，把文件提交到仓库，`-m` 后面输入的是本次提交说明的内容，为什么 Git 添加文件需要 `add`，`commit` 两步？因为 `commit` 可以一次提交很多文件，所以可以多次 `add` 不同的文件。

```
git commit -m"write a readme file"
```

此时开始工作，去修改 `readme.txt` 里面的内容，修改了之后查看一下当前的状态(因为 Git 时刻记录着本仓库中代码的修改情况)，很明显地知道当前的 `readme.txt` 被修改了（modified:   readme.txt），但还没准备提交修改

```
git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

但是这里只是知道这个文件有被修改了，并不知道修改了什么内容，于是就可以通过这个命令来查看

```
git diff readme.txt
diff --git a/readme.txt b/readme.txt
index d8036c1..3d28bae 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,2 @@
-Git is a version control system.
+Git is a version control system.by lzy
 Git is free software.
\ No newline at end of file
```

修改之后的文件，再把它提交到仓库，提交的修改和提交新文件是一样的两步先 `add`

```
git add readme.txt
```

现在再来查看一下当前的状态，就会看到将要被提交的修改包括 `readme.txt`

```
git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   readme.txt
```

下一步就可以放心去提交了，然后再查看当前仓库的状态，告诉我没已经没有需要修改的提交了，并且工作目录很干净！（working tree clean）

```
git status
On branch master
nothing to commit, working tree clean
```

我们每次修改提交修改提交，就会形成了不同的版本了，此时我们可以通过命令查看不同的版本

```
git log
commit 7a4c0baf8cdd609c941257fb863ecbbf5045b4fe (HEAD -> master)
Author: lzycheer <1289926841@qq.com>
Date:   Sat Jun 20 17:06:46 2020 +0800

    I learn git

commit eb94719a44fdc6e9a320de2cbc9c5985750fc1a2
Author: lzycheer <1289926841@qq.com>
Date:   Sat Jun 20 17:00:49 2020 +0800

    add by lzyan

commit d4308636fb7bcbd4ef9a4c6468940764ae4f323e
Author: lzycheer <1289926841@qq.com>
Date:   Sat Jun 20 16:30:12 2020 +0800

    wrote a readme file

```

还可以加上参数来减少信息的输出，这些一大串的 `7a4c0...` 是 `commit id（版本号）`，使用 `sha1` 计算出来的数字，因为 `Git` 是分布式版本管理系统，在多人协作同一个版本库里工作时用来区分版本号冲突。而其中带有 `HEAD` 就是当前的版本，`HEAD^` 就是上个版本以此类推

```
git log --pretty=oneline
7a4c0baf8cdd609c941257fb863ecbbf5045b4fe (HEAD -> master) I learn git
eb94719a44fdc6e9a320de2cbc9c5985750fc1a2 add by lzyan
d4308636fb7bcbd4ef9a4c6468940764ae4f323e wrote a readme file
```

现在回到上一个版本，也就是 `add by lzyan` 版本

```
git reset --hard HEAD^
HEAD is now at eb94719 add by lzyan
```

此时在看一下版本库的状态，可以发现最新的那个版本已经不见了。时光穿梭

```
git log
commit eb94719a44fdc6e9a320de2cbc9c5985750fc1a2 (HEAD -> master)
Author: lzycheer <1289926841@qq.com>
Date:   Sat Jun 20 17:00:49 2020 +0800

    add by lzyan

commit d4308636fb7bcbd4ef9a4c6468940764ae4f323e
Author: lzycheer <1289926841@qq.com>
Date:   Sat Jun 20 16:30:12 2020 +0800

    wrote a readme file
```

最新的版本不见了，此时又想回去呢，那就得需要上前去查看最新版本的那个 `commit id` 了，然后在用指令回去，此时后面的版本号不用打全，因为 `git` 会帮你去找

```
git reset --hard 7a4c0
HEAD is now at 7a4c0ba I learn git
```

如果你当前已经关闭过窗口，找不到版本号了，那也是没关系，通过指令查看你使用过的命令

```
git reflog
7a4c0ba (HEAD -> master) HEAD@{0}: reset: moving to 7a4c0
eb94719 HEAD@{1}: reset: moving to HEAD^
7a4c0ba (HEAD -> master) HEAD@{2}: commit: I learn git
eb94719 HEAD@{3}: commit: add by lzyan
d430863 HEAD@{4}: commit (initial): wrote a readme file
```

### 总结本次提到的Git指令

```
git init //初始化，把目录变成Git管理的仓库，生成.git文件
git add <fileName>//把文件添加到仓库
git commit -m"you meg" //把文件提交到仓库，并且添加描述的信息
git status //查看当前的状态，比如修改了哪些文件
git diff <fileName> //查看文件修改了什么内容，是比较工作区与暂存区的区别
git diff --cached是比较暂存区与版本库的区别。（扩展）
git log //查看不同的版本信息
git log --pretty=oneline //只查看比较重点的版本信息

git reset --hard HEAD^/<commit id> //回到上一个版本和回到指定的版本
git reflog //查看你使用过的命令

```

## 认识一些概念

- 工作区

在电脑里能看到的目录就是所谓的工作区，比如 `learngit` 文件夹就是一个工作区。

- 版本库(Repository)

工作区隐藏了一个 `.git` 目录，这个不算工作区，而是 `Git` 的版本库。

`Git` 版本库里存放了很多东西，其中最重要的就是 `stage`（或者叫index）的暂存区，还有 `Git` 自动闯创建的第一个分支 `master`，以及指向master的一个指针叫HEAD。

![image-20200620175135934.png](https://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832916375306502144.png)

前面讲到把文件往 `Git` 版本库里添加的时候，是分两步执行：

第一步是用 `git add` 把文件添加进去，实际上就是把文件修改添加到暂存区；

第二步是用 `git commit` 提交更改，实际上就是把暂存区的所有内容提交到当前分支。

因为创建 `Git` 版本库的时候，`Git` 自动创建了唯一一个 `master` 分支，所以现在 `git commit` 就是往 `master` 分支上提交更改的。可以理解为需要提交的文件修改通通放到暂存区，然后一次性提交暂存区的修改。

## 防止扣奖金

工作中难免会犯点小错，当你修改了某个文件的时候，`Git` 已经记录了你修改的记录，当你把修改的部分该回来之前的版本，此时使用 `git status` 你还是会看到 `Git` 告诉你已经修改过了。然而现在撤销修改就能帮上你的忙了(此时文件还没有 `git add` )

```
git checkout -- <fileName>
```

这个命令的意思就是把文件在工作区的修改全部撤销，有两种情况：

一种是 `readme.txt` 自修改后还没有被放到暂存区，现在撤销修改就回到和版本库一模一样的状态;

二种是 `readme.txt` 已经添加到暂存区后，又作了修改，现在撤销修改就回到添加到暂存区后的状态。

总之，就是这个文件返回到最近一次 `git commit` 或 `git add` 的状态

第二种的情况，就是 `git add` 到了缓存区了，庆幸是还没有 `commit`，然后就可以使用下面的命令来把缓存区的修改撤销掉（unstage），重新放回到工作区。`git reset` 命令既可以回退版本，也可以把暂存区的修改退回到工作区。当使用 `HEAD` 时，表示是最新的版本。

```
git reset HEAD <filename>
```

上面是撤销修改，而此时你想要把完整的文件删除。此时你已经修改了文件并 `git add` 到缓存区且 `git commit` 到了本地仓库。

```
git add test.txt
git commit -m"to add test.txt"
```

或许你会在本地通过 `rm` 来删除这个文件

```
rm test.txt
```

因此你的工作区与版本库不一致了，`git status` 之后就会告诉你删除了哪些文件，此时你可以用命令 `git rm` 删掉版本库中的此文件，并且 `git commit`

```
git rm test.txt
rm 'test.txt'

git commit -m"remove test.txt"
···
```

## 远程仓库

### 1. 认识远程仓库，使用github远程仓库

`Git` 是分布式版本控制系统，同一个 `Git` 仓库，可以分不到不同的机器上。而这个做原始版本库的机器可被别的机器“克隆”这个原始版本库，但我们这学习者就不会拿一台机器从当服务器，24小时不关机，来做一个原始的版本库机器，可以自己搭但是没必要。`github` 上面就有提供这样的一个服务，只要注册一个账号就可以获得 `Git` 远程仓库。

由于本地 `Git` 仓库和 `Github` 仓库之间的传输是通过 `SSH` 加密的，所以需要一些设置：

第1步：创建 `SSH Key`。在用户主目录下，看看有没有 `.ssh` 目录，如果有，再看看这个目录下有没有 `id_rsa` 和 `id_rsa.pub` 这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开 `Shell（Windows下打开Git Bash）`，创建 `SSH Key`：

```
ssh-keygen -t rsa -C "youremail@example.com"
```

你需要把邮件地址换成你自己的邮件地址，然后一路回车，使用默认值即可，由于这个 `Key` 也不作用其机密事项，所以也无需设置密码。

如果一切顺利的话，可以在用户主目录里找到 `.ssh` 目录，里面有 `id_rsa` 和 `id_rsa.pub` 两个文件，这两个就是 `SSH Key` 的秘钥对，`id_rsa`是私钥，不能泄露出去，`id_rsa.pub`是公钥，可以放心地告诉任何人。也可以使用以下命令查看：

```sh
cat ~/.ssh/id_rsa.pub
```

![image-20200621170147551.png](https://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832916598988734464.png)

登录 `github` 打开此界面设置 `SSH Keys`，填上任意的 `title` ，和 `id_rsa.pub` 文件的内容

![](https://resource.lzyan.fun/PigGo/20211202163401.png)

点击 `Add SSH Key` 之后就可以看到添加成功了 `Key`!

![](https://resource.lzyan.fun/PigGo/20211202163317.png)

为什么 `GitHub` 需要 `SSH Key` 呢？因为 `GitHub` 需要识别出你推送的提交确实是你推送的，而不是别人冒充的，而 `Git` 支持 `SSH` 协议，所以，`GitHub` 只要知道了你的公钥，就可以确认只有你自己才能推送。

当然，`GitHub` 允许你添加多个 `Key`。假定你有若干电脑，你一会儿在公司提交，一会儿在家里提交，只要把每台电脑的 `Key` 都添加到 `GitHub`，就可以在每台电脑上往 `GitHub` 推送了。

最后友情提示，在 `GitHub` 上免费托管的Git仓库，任何人都可以看到喔（但只有你自己才能改）。所以，不要把敏感信息放进去。

如果你不想让别人看到 `Git` 库，有两个办法，一个是交点保护费，让 `GitHub` 把公开的仓库变成私有的，这样别人就看不见了（不可读更不可写）。另一个办法是自己动手，搭一个 `Git` 服务器，因为是你自己的 `Git` 服务器，所以别人也是看不见的。

### 2. github添加远程仓库

在 `github` 上面打开仓库 `repositories` 然后仓库，填写名字，然后其他配置默认就可以了

![image-20200621223155458.png](https://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832916902962528256.png)

然后点击 `create repository` 创建成功，目前的仓库是空的。`github` 告诉我们，可以从这个仓库克隆出新的仓库，也可以把一个已有的仓库与之关联，然后把本地的仓库内容推送到 `github` 仓库。

![image-20200621223407747.png](https://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832916997946736640.png)

在本地仓库下运行命令，远程库的名字就是 `origin`，这是 `Git` 默认的叫法，也可以改成别的，但是 `origin` 这个名字一看就知道是远程库。

```
git remote add origin https://github.com/YCheer/learngit.git
```

然后把本地仓库的所有内容推送到远程仓库上，用 `git push` 命令把内容推到远程仓库，实际上是把当前分支 `master` 推送到远程仓库。而由于远程仓库是空的，第一次推送 `master` 分支时，加上参数 `-u`，`Git` 不但会把本地仓库的 `master` 分支内容推送到新的 `master` 分支，还会把本地的 `master` 分支和远程 `master` 分支关联起来，以后的推送或者拉取就可以简化命令。

```
git push -u origin master
```

于是乎可以在 `github` 上面看到跟本地仓库一摸一样的仓库。
![image-20200621232231270.png](https://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832917131568873472.png)

然后以后本地提交只需要以下命令就可以，把本地 `master` 分支的最新修改推送到 `github`，现在，就拥有了真正的分布式版本库了。

```
git push origin master
```

### 3. 从远程仓库克隆

在 `github` 上面新建一个仓库 `gitskill`，勾选 `Initialize this repository with a README`，`github` 会自动创建一个 `readme.md` 文件

![image-20200621233859506.png](https://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832917232605462528.png)

然后就是在本地克隆下来了，找个合适的位置

```
git clone git@github.com:YCheer/gitskill.git
```

于是 `gitskill` 这个仓库和里面的文件就下来了

![image-20200621234144340.png](https://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832917335042949120.png)

## 分支管理

### 1. 先认识以下分支的道理

分支在实际中有什么用呢？假设你准备开发一个新功能，但是需要两周才能完成，第一周你写了50%的代码，如果立刻提交，由于代码还没写完，不完整的代码库会导致别人不能干活了。如果等代码全部写完再一次提交，又存在丢失每天进度的巨大风险。

现在有了分支，就不用怕了。你创建了一个属于你自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作。

其他版本控制系统如 `SVN` 等都有分支管理，但是用过之后你会发现，这些版本控制系统创建和切换分支比蜗牛还慢，简直让人无法忍受，结果分支功能成了摆设，大家都不去用。

但 `Git` 的分支是与众不同的，无论创建、切换和删除分支，`Git` 在 1 秒钟之内就能完成！无论你的版本库是 1 个文件还是 1 万个文件。

有关实际项目中如何使用 `Git` 分支管理，这位博主描述得挺不错，先观摩观摩https://blog.csdn.net/ShuSheng0007/article/details/80791849

还要明白的一个概念就是，以上面创建的仓库为例目前只有一条 `master` 分支，`Git` 用 `master` 指向最新的提交，再用 `HEAD` 指向 `master`，就能确定当前分支，以及当前分支的提交点

![image-20200622002443580.png](https://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832917451040620544.png)

### 2. 实践

创建一个 `dev(develop)` 分支，然后切换到 `dev` 分支

```
git checkout -b dev
```

其中 `-b` 表示创建并且切换，相当于两条命令

```
git branch dev
git checkout dev
```

然后查看一下当前分支，含 `*` 表示当前所在的分支

```
git branch
* dev
  master
```

然后在这个 `dev` 分支上做正常的提交操作，提交完成后切回 `master` 分支

```
git checkout master
```

此时查看一下添加的内容，你会发现添加的内容不见了，那是因为提交是在 `dev` 分支上，而 `master` 分支此刻的提交点并没有变化，现在把 `dev` 分支的工作成果合并到 `master` 分支上，`git merge` 命令用于合并指定分支到当前的分支。

```
git marge dev
Updating e028fd0..e391d83
Fast-forward
 readme.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

合并后查看修改的内容，就可以看到和 `dev` 分支提交的一摸一样了，注意到上面的 `Fast-forward` 信息，`Git` 告诉我们这次合并是“快速模式”，也就是直接把 `master` 指向 `dev` 的当前指向，所以合并速度非常快。

合并完成后，那就可以放心删除 `dev` 分支了，因为创建、合并和删除分支非常快，所以 `Git` 鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在 `master` 分支上工作效果是一样的，但过程更安全。

```
git branch -d dev
Deleted branch dev (was e391d83).
```

我们注意到切换分支使用`git checkout <branch>`，而前面讲过的撤销修改则是`git checkout -- <file>`，同一个命令，有两种作用，确实有点令人迷惑。

实际上，切换分支这个动作，用`switch`更科学。因此，最新版本的Git提供了新的`git switch`命令来切换分支，创建并切换到新的`dev`分支，可以使用

```
git switch -c dev
```

直接切换到已有的`master`分支，可以使用

```
git switch master
```

### 3. 总结本次用到的一些新命令

```
git branch //查看分支
git branch <name> //创建分支
git checkout <name>或git switch <name> //切换分支
git checkout -b <name>或git switch -c <name> //创建+切换分支
git merge <name> //合并某分支到当前的分支
git branch -d <name> //删除某分支
```

## 解决分支的突出问题

这里演示的冲突情况是，新建立一个分支在分支上面修改了文件内容提交了，然后回到 `master` 分支，再修改一次文件内容再提交，这时候再进行分支的合并，然后合并他们，`Git` 会告诉你无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突，然后就必须手动解决冲突后再提交。此时用 `git status` 可以告诉你冲突的文件，查看修改过的文件，带有 `<<<<<` 和 `>>>>>` 的标记了不同的分支内容，然后修改一下再提交，然后两个分支就合并了。现在可以用带参数的 `git log` 命令查看分支的情况，最后就可以删除新建的分支了。

```
git checkout -b feature1  //新建并切换到feature1分支
Switched to a new branch 'feature1'

git branch //查看当前的分支
* feature1
  master

vi readme.txt //编辑一下文件

cat readme.txt //查看这个文件
Git is a version control system.by lzy I want to learn git .
Git is free software. in the dev branch.
creating a new branch is quick and simple //这行是编辑了的内容

git add readme.txt

git commit -m"add simple" //添加并提交
[feature1 6cdbd23] add simple
 1 file changed, 1 insertion(+)

git checkout master //切换到master分支
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

vi readme.txt //在master分支下再编辑一下文件

git add readme.txt

git commit -m"& simple" //添加并且提交
[master c9a927d] & simple
 1 file changed, 1 insertion(+)

git merge feature1 //合并分支
Auto-merging readme.txt
CONFLICT (content): Merge conflict in readme.txt
Automatic merge failed; fix conflicts and then commit the result.//提示 readm.txt文件冲突

git status //命令告诉我们冲突的文件
On branch master
Your branch is ahead of 'origin/master' by 2 commits.
  (use "git push" to publish your local commits)

You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)

        both modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")

cat readme.txt //Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容
Git is a version control system.by lzy I want to learn git .
Git is free software. in the dev branch.
<<<<<<< HEAD
creating a new branch is quick & simple.
=======
creating a new branch is quick and simple
>>>>>>> feature1

vi readme.txt //在master分支下再修改这个文件下的内容为Creating a new branch is quick and simple

git add readme.txt

git commit -m"conflict fixed" //添加并提交
[master ff68119] conflict fixed


git log --graph --pretty=oneline --abbrev-commit //此命令看分支的合并情况
*   ff68119 (HEAD -> master) conflict fixed
|\
| * 6cdbd23 (feature1) add simple
* | c9a927d & simple
|/
* e391d83 in the dev branch test
* e028fd0 (origin/master) add test.txt
* 1183990 to delete lzyan.txt
* 018a0e8 add lzyan.txt
* 33018d1 to delete test.txt
* abfc53b add test.text
* 5429a26 remove test.txt
* 2edca55 add test.txt
* 7a4c0ba I learn git
* eb94719 add by lzyan
* d430863 wrote a readme file

git branch -d feature1 //合并成功 删除新建的分支
Deleted branch feature1 (was 6cdbd23).

```

## 分支的策略管理（合并分支后生成一次提交commit）

通常合并分支的时候，`Git` 会用到 `Fast forward` 模式，但这种模式下，删除分支后，会丢掉分支的信息。如果要强制禁用 `Fast forward` 模式，`Git` 就会再 `merge` 时生成一个新的 `commit`，这样子再分支历史上面就可以查看出分支信息。

首先就是创建新的分支然后修改并且添加提交，切回到 `master` 分支去合并，而此时的 `git merge` 合并带有参数，`-m` 参数就是把 `commit` 描述写进去，`--no-ff` 表示禁用 `Fast forward`

```
git merge --no-ff -m "merge with no-ff" dev
```

合并后用 `git log` 查看分支的历史

```
git log --graph --pretty=oneline --abbrev-commit
*   fe5e369 (HEAD -> master) merge with on--
|\
| * 39857cb (dev) add merge
|/
*   ff68119 conflict fixed
|\
| * 6cdbd23 add simple
* | c9a927d & simple
|/
* e391d83 in the dev branch test
* e028fd0 (origin/master) add test.txt
* 1183990 to delete lzyan.txt
* 018a0e8 add lzyan.txt
* 33018d1 to delete test.txt
* abfc53b add test.text
* 5429a26 remove test.txt
* 2edca55 add test.txt
* 7a4c0ba I learn git
* eb94719 add by lzyan
* d430863 wrote a readme file
```

在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，`master` 分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

那在哪干活呢？干活都在 `dev` 分支上，也就是说，`dev` 分支是不稳定的，到某个时候，比如1.0版本发布时，再把 `dev` 分支合并到 `master` 上，在 `master` 分支发布 1.0 版本；

你和你的小伙伴们每个人都在 `dev` 分支上干活，每个人都有自己的分支，时不时地往 `dev` 分支上合并就可以了。

所以，团队合作的分支看起来就像这样：

![image-20200622102859060.png](https://resource.lzyan.fun/lzyan_blog_system/2021_04_17/png/832917569391296512.png)

## 中途需要修改bug的时候该怎么利用分支

修复 `bug` 时，我们会通过创建新的 `bug` 分支进行修复，然后合并，最后删除；

当手头工作没有完成时，先把工作现场 `git stash` 一下，然后去修复 `bug`，假定需要在 `master` 分支上修复，就从 `master` 创建临时的分支，修改完成后合并一下，修复后，再 `git stash pop`，回到工作现场；

在 `master` 分支上修复的 `bug`，此时的 `dev` 分支下面的 `bug` 是还没有解决的，想要合并到当前 `dev` 分支，可以用 `git cherry-pick <commit>`命 令，把 `bug` 提交的修改“复制”到当前分支，避免重复劳动。

如果说，开发一个新的功能，最好新建一个分支 `featrue`，如果要丢弃一个没有被合并过的分支，可以通过 `git branch -D <name>` 强行删除。

## 多人协作

多人协作的工作模式通常是这样：

1. 首先，可以试图用 `git push origin <branch-name>` 推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用 `git pull` 试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用 `git push origin <branch-name>` 推送就能成功！

如果 `git pull` 提示 `no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令 `git branch --set-upstream-to <branch-name> origin/<branch-name>`。

这就是多人协作的工作模式，一旦熟悉了，就非常简单。


## 给提交打标签

发布一个版本时，我们通常先在版本库中打一个`标签（tag）`，这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。

`Git` 的标签虽然是版本库的快照，但其实它就是指向某个 `commit` 的指针（跟分支很像对不对？但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。

管理标签

- 命令 `git tag <tagname>` 用于新建一个标签，默认为`HEAD`，也可以指定一个commit id；
- 命令 `git tag -a <tagname> -m "blablabla..."` 可以指定标签信息；
- 命令`git tag`可以查看所有标签。

操作标签

- 命令 `git push origin <tagname>` 可以推送一个本地标签；
- 命令 `git push origin --tags` 可以推送全部未推送过的本地标签；
- 命令 `git tag -d <tagname>` 可以删除一个本地标签；
- 命令 `git push origin :refs/tags/<tagname>` 可以删除一个远程标签。

---
layout:     post
title:      "《Git学习指南》笔记"
date:       2018-5-25
author:     "danaweng"
header-style: text
catalog: true
tags:
    - 编程
    - Linux
    - Git
    - 笔记
---

## 1章 基本概念
首先应该知道集中式版本控制系统和分布式版本控制系统的差别：

**集中式：**所有的版本需要放在服务器上，用户每次写代码的时候都需要从服务器中获得最新的版本才能工作，获得的只是一份最新版本的代码，如果服务器不能连接上，就不能提交，还原，对比等等。归根到底还是把所有的东西都存在了服务器。还有可以这么理解，服务器上存在着所有的版本，每次其他的客户端都必须联网才能访问这个服务器，这样才能获得相应的版本信息，才可以进行提交，对比，还原，其实就是一个线上才能使用的系统感觉。所有的服务都基于联网,所有人共享一个通用的版本库。

![集中式版本管理系统](/img/in-post/post-introduction-to-git/centralized_version_management_system.png)

**分布式：**每台机器都克隆了一份含有所有内容的版本库，包括版本信息。

![分布式版本管理系统](/img/in-post/post-introduction-to-git/distributed_version_management_system.png)

这里有一篇介绍不同版本控制的文章，可以参考下，[几种常用的版本控制系统优缺点比较
](https://www.jianshu.com/p/e8528bff9b1b)


版本库分为项目版本库<font color="#F27F05"> blessed repository</font>,共享版本库 <font color="#F27F05">shared repository</font>,工作流程版本库<font color="#F27F05"> workkflow repository</font>以及派生版本库 <font color="#F27F05">fork repository</font>.

版本库的实质：高效的数据存储结构，由文件(**blob**)，目录(**tree**)，版本组成(**commit**)
所有的数据都会被计算成为一个16进制的散列值，这个相关对象的散列值将会被用作相关对象的引用。

> 通过git可以协调某一种功能的并行开发建立显示分支，可以进行分支的移植以及捡取操作，还可以进行分支历史的改造排序以及删除从而建立更好的历史文档。

## 2章 入门

工作区:对于一个带版本库的目录，我们称之为工作区**(working tree)**

`git config --global user.email "danaweng@gmail.com" `//配置用户邮箱

`git config --global user.name "danaweng" `//配置用户名

`git help orderName` //查询某条命令的文档

`git init` //在目录中创建一个.git的隐藏目录，并在其中创建一个版本库，版本库中将项目历史进行存储。


`git add file1 file2..;` //提交修改到暂存区，也就是我们所说的<font color="#F27F05">
index</font>，确定了哪些文件应该被包含在下次的提交中

`git commit --message "";` //提交修改到版本库中，并赋予此次提交一个散列值以便标记这次新提交

`git status;` //状态检查

`git diff filename`//查看文件被修改的内容

`git log;`//显示历史

`git log --oneline;`显示单行记录

`git log --graph;`显示图形化记录

**`git log  --oneline -- note.txt`**//注意这里有个空格‘-- filename’，用来显示某个文件的提交历史

**`git log  --author=danaweng -10 -- note.txt`**//显示10条作者为danaweng的提交记录

`git rm file`; //这里的话移除文件再添加相同的文件，git status的话会显示文件未添加到版本管理中，再add的话，此时不会有文件添加的信息，应该是之前本地库就有信息了，rm并没有删除该记录。

`git clone ./ ../dana `//进入git的项目后将该项目克隆到dana的文件夹中

`git pull` //若是git clone后的项目用这个命令，那么会自动把原版本的新提交传递给它的克隆体，这是因为克隆版本库的时候就存储了原版本库的信息，这个过程也是所谓的合并，和fetch不同，会进行一次merge，产生一次合并的新提交。


**git pull 与git push的区别<br>**
`git pull pathName` //push的话更加适用于裸版本库，即不带工作区的版本库，也就是开发者传递提交的汇聚点（push命令的汇聚点），相当于一个中转站，便于其他人从中拉去他们所做的修改。若进行push的时候刚好有人在我们之前做过了push操作，那么此时的push会被拒绝，此时需要先一次pull。这里的意思也就是说push命令需要保证自己的库和最新bare库是一致的，否则的话会报错，错误如下：

<font color="880000">By default, updating the current branch in a non-bare repository is denied, because it will make the index and work tree inconsistent with what you pushed, and will require 'git reset --hard' to match the work tree to HEAD</font>

.**默认情况下是不能够对非bare库进行push。**所以对于非bare库来说，只能用pull命令。

`git clone --bare pathName`//创建裸版本库


## 3章 提交是什么
提交：add,commit命令创建一次提交，每次提交都会产生一个hash值。

commit hash 由40个字符组成，根据文件内容以及元数据（作者，提交时间）计算出来

`git fsck`//file system check,检查和维护不一致的文件系统

`git checkout 9acc`（9acc是某个commit hash 的缩写形式） 

` git checkout release-1.2.3`
 
版本库中包含的不仅仅是一个个独立的提交，同时也存储了这些提交之间的关系。

`git diff 77d23 HEAD`//查看不同当前版本与77d23版本之间的差别

**`git diff 77d23^! `**//与上一次提交进行比较

**`git diff 77d23 HEAD - dir1/dir2/ `** //限制哪些文件或者目录之间的差异

`git diff --stat 77d23 HEAD` 显示每个文件中的修改数量

`git log -n 3`

`git log --oneline`

`git log --stat` //statistics,用来显示被修改的那些文件

`git log --dirstat` //显示包含被修改文件的目录

`git log --shortstat` //显示项目中有多少文件被修改以及新增删减了多少文件

`git log --graph` //图形化显示各提交之间的关系


## 4章 多次提交

工作区 暂存区（**staging area**） 版本库 

`git reset`这个命令如果针对的是只是部分而不是全部，例如git reset 211880 zml.txt 此时只会把211880版本的内容重新覆盖到zml.txt中，新增加的内容不会删除（这是因为默认的情况下加了--mixed选项），且提交历史不会改变，
所以这个操作完成后zml.txt的状态是属于被修改的状态，如果是 git reset 211880的话，同样用211880的内容覆盖，但是新增的内容不会删除，此时提交历史会回到原来的211880,如果要完全覆盖成原来的内容而
不保留现在新增的修改的话，那么应该使用revert。<font color="#880000">reset是在正常的commit历史中,删除了指定的commit,这时 HEAD 是向后移动了,
而revert是在正常的commit历史中再commit一次,只不过是反向提交,他的 HEAD 是一直向前的.</font>
具体见[revert和reset的使用](https://samael65535.github.io/2013-01-18/git/)

git reset 是把HEAD向后移动了一下，而git revert是HEAD继续前进，只是新的commit的内容和要revert的内容正好相反，能够抵消要被revert的内容。理解这句话很重要，这个是重点，reset是直接把原来的提交删除了，回到了原来的状态，
而revert是做了一次逆向提交，就相当于把东西回到指定的版本之后再次提交（比如最新的版本多了三个文件，要回退的版本没有这三个文件，那么revert的做法是把三个文件删除后提交，而reset是删除了之前的提交）
当你回滚之后，又后悔了，想恢复到新的版本怎么办？
用`git reflog`打印你记录你的每一次操作记录,此时可以拿到你的操作记录，可以得到回滚之前的commit值，然后就可以用reset或者revert操作了。
而checkout对于快速查看项目旧版本来说非常有用。上面说的可能也不是很清楚，找到了一个很清楚的链接
[代码回滚：Reset、Checkout、Revert 的选择](https://github.com/geeeeeeeeek/git-recipes/wiki/5.2-%E4%BB%A3%E7%A0%81%E5%9B%9E%E6%BB%9A%EF%BC%9AReset%E3%80%81Checkout%E3%80%81Revert-%E7%9A%84%E9%80%89%E6%8B%A9)。
> 相比 git reset，它不会改变现在的提交历史。<font color="880000">因此，git revert 可以用在公共分支上，git reset 应该用在私有分支上。你也可以把 git revert 当作撤销已经提交的更改，而 git reset HEAD 用来撤销没有提交的更改。</font>

`git reset HEAD filename` //git add filename 相反的操作

`git status --short` //status 输出更为紧凑

`git add file1 file2 file3`

`git add dir/`

`git add dir/file`

`git add .`//可以使用 --interactive选项，可以单独选取某段代码甚至逐行将代码注册到提交中

`git commit` 后暂存区就会被清空，工作区则将没有影响

`git diff `//暂存区与工作区之间的不同

`git diff --staged `//当前版本库中HEAD提交与暂存区中间的不同

`git reset HEAD .` //重置暂存区

`git reset HEAD filename or filepath`

.gitignore文件中的条目只能影响当前**还未交给git管理的文件**，若想忽略一个已经被版本化的文件，那么可以通过update-index命令的--assume-unchanged选项来做到这一点。
.gitignore可以在指定的文件中指定这些文件路径和目录，并且使用*，&等通配符。如果文件中写的是"generated/"这样子的简单路径，也会将所有包含这一个名字的目录都包含在内，例如src/demo/generated都
会被彻底忽略，若在这类路径之前加个/，例如/generated/，那么就是只有这个确切路径（相对根目录而言）会被忽略。

**`git stash`**  //某些事情进行到中间的时候发现自己需要**快速修复某个问题**，比如file1文件已经有部分新功能实现了，还没测试，这个时候要先修复原来file1中的一个bug,
这时先把实现部分功能的file1 add操作，然后再stash操作，这时会把修改存储在储藏栈(stash stack)的缓冲区中。
然后这个时候file1就会回到原来还没有进行添加新功能的状态，这时可以修复bug，再add再stash pop操作。这里自己测过了，stash只针对暂存区。
注意，这里的话最新的stash是以stash@{0}表示的，还有就是创建stash的时候如果没有提交信息，那么会以自己上一次提交的信息作为stash的提交信息。

`git stash pop`//恢复栈顶的被储存的修改

`git stash list`//看下存储堆栈中有什么

`git stash pop stash@{1}`//恢复更早之前所做的存储




## 第5章 版本库

了解Git的存储和组织数据的方式，可以对工作流有一个更好的理解。Git由顶层和底层结构组成，顶层结构就是我们常用的瓷质命令（**porcelain command**，如log,reset,commit等），
底层结构称之为管道（plumbing），这是一组带有少量选项的命令，瓷质命令就是以此为基础构建的，但是管道命令很少用到。

对象数据库：所有提交中的文件，目录，相关的元数据都将被存储在该数据库中。

**Git的核心是一个对象数据库**，可用存储文本或者二进制数据。对象数据库是一个高效的实现，非常适用于那些拥有大量小型源文件的项目。

`git hash-object -w hello.txt` //将对象插入到数据库中

`git cat-file -p hello.txt's commit hash `//访问这个插入对象

Git使用了一种包含两种节点类型的简单树结构，文件以**blob对象**形式按字节存进对象数据库，目录将使用**tree对象**来表示。git对相同的数据只存储一次，若一份文件是另外一份文件的copy，即使文件名不同，得到的散列值也是相同的，
这样节省内存同时提高性能，**Git操作之所以快，是因为算法只比较相关的散列值，**不需要查看具体的实际数据。

git可以压缩相似的内容，当新文件和已有的文件只是几行区别的时候Git可以采用**增量方法**来存储这些文件，
这种情况下包文件中将只存储原始版本后来被改变的那一部分。要做到这点，就需要在想节省空间时使用gc命令，这样git会删除所有的多余的，
不接受任何分支头访问的提交，并将剩下的提交储存到包文件中，这对于源代码占据绝大多数的项目来说实现了一种高效的压缩。

hash collision是一种非常罕见的事件，一旦发生，git无法通过散列值来识别内容，但是散列值的可能取值至少有2的160方中，因此不必担心。
我们的历次提交也被存储在对象数据库中，可以通过
git cat-file -p 64b98df0  进行查看，tree独享负责描述改提交的内容，parent指的是他的上一次提交。

关于移动，重命名文件:<br>
在svn中可以通过svn move来移动文件，但是若在图形届界面中移动文件到新的位置后，那么svn会认为是先进行了文件的删除在新建文件，
在git中则不同，它采用了重命名检测算法，若文件消失了，还会存在前次提交中，淡有相同名字或者相似内容的文件出现在了另外的位置,git会自动检测到，
便会假定该文件移动过了。可以用log的-M(即move)选项来激活重命名算法，通过--summary来对文件修改的相关信息格式化显示，还可以用grep来对输出进行筛选，
另外百分比显示了源文件和目标文件的相似度。
git log --summary -M90% | grep -e “^ rename”
对于被重新命名的文件，可以使用--follow选项跟踪重命名文件的历史记录。如果不用改选项，日志就会在该文件被重新命名的那一刻停止。
git log --follow foo-rename.txt
同理，git log --summary -C90% | grep -e "^ copy"//跟踪被复制的文件
我们可以将重命名检测配置成默认的选项，这样一来就无需每次使用Log命令的时候为其制定-M以及--follow选项了。

`git config diff.renames true`

`git blame filename `//确定某段代码来源

当我们将某些大的代码块复制或者移动到其他的文件中时，git甚至可以确定其中某几行代码的来源，blame命令还可以显示出最后一次修改这几行代码的人以及修改的时间。

`git blame -M -C -C -C copied-together.txt`

-M暗示的是文件的移动和复制操作，-C用于检测相同提交中的文件副本，可以用多个-C来搜索改文件在更多提交中的副本。
对大型版本库来说，这种操作有时候会需要比较长的时间。



## 6章 分支

需要分支的原因：<br>
> 1.有2个以上开发者对同个项目进行并行开发 
> 
> 2.为修复旧版本中的bug而必须要创建和发布新的版本（A->B->C）比如B中发现了Bug,
> 新版本的开发C还没有完成交付，这时只能基于B开一条新分支D来进行修复。

`git branch` //列出当前所有分支，带*的分支就是当前活跃分支

`git checkout branch_name` //改变当前活跃分支

`git branch branch_name` //创建分支

`git branch branch_name 38b7da45e` //为任意一次提交创建新的分支

`git branch new_branch old_branch` //从旧分支中创建新的分支

使用切换分支，要用到命令checkout

`git checkout a_branch` //切换到a分支

`git checkout -b a_branch` //快捷方式，创建并切换新的分支

如果是自己在本地同个仓库创建新的分支，那么在新分支上进行的修改就是所有分支的修改，因为HEAD指针指向多个分支，这些分支的指针都是一样的，比如master,new1,new2共三个分支，这些分支其实是一样的。什么情况下不一样，应该就是从远程库拉下来的分支，这些分支可能是其他人创建的。

有时切换分支的时候会失败，比如现在你在工作区域进行了修改但是没有提交，这个时候如果切换分支的话会导致信息丢失，
（工作区的话就是一张纸，每次都是记录当前的开发路线的内容，不会进行保留之前的东西，所以如果需要保留的话需要commit或者stash），有3种做法进行处理，
一是提交修改并切换

```
git commit --all
git checkout a_branch

```

二是放弃这部分的修改并进行强行切换

`git checkout --force a_branch`

三是用stash命令先将这部修改存储起来然后再进行切换，之后在用stash pop命令进行恢复

```
git stash
git checkout a_branch
git stash pop
```
`git reset --hard 39ea21a` //将分支指针重置到39ea21a所在的活动分支上，--hard用于确保工作区和暂存区也会被设置为提交39ea21a的状态，因此如果不想覆盖工作区和暂存区中的内容，最好重置前先用git stash命令存储下修改

`git branch -d a_branch `//删除已经终止的分支

`git branch -D a_branch `//删除一个打开的分支

git删除分支的时候只是删除了指向相关提交的指针，提交对象还会留在版本库中，若知道删除分支时的散列信息就可以将已经删除的分支恢复过来。

`git reflog` //想知道恢复分支的提交散列值，可以通过reflog找出来

gc是垃圾回收，可用于清理版本库，移除所有不属于当前分支的提交对象。对于那些不属于任何分支前身的提交，可以视为垃圾用gc将其清理.如果我们想进一步清理自己的版本库，可以先将它克隆一份，并删除其源版本库。//这个地方我也暂时不是很懂，先这样记着吧


## 7章 合并分支

`git merge a_branch` //合并分支a_branch

git的自动合并原理：a,orgin,b三份文件，其中a,b分别是分支a,b上对orgin文件的修改，
这个时候怎么知道要如何合并a,b文件呢？主要依据，根据**最后一个共同的祖辈提交**orgin来与眼前的2个分支版本对比下就知道要用a,还是b了，
比如orgin中有个地方没有改动，b也没有改动，只有a改动过，那么就用a的，假如a,b都改动过，会产生冲突，就要手动合并了(两个开发者)。
当然找到共同祖辈提交不是容易的事情，为此Git实现了3种不同的合并算法，**默认用递归算法**。还实现了经典的三路算法和octopus算法，octopus可以同时处理多个分支。
[这里也不是很清楚其他三种算法的区别，先记着吧]

由于历史原因，冲突标志通常会描述两组修改，当前分支以及另外一个分支的内容，共同祖辈在默认情况下不显示，但是可以将其配置设置成3路显示格式。

`git config merge.conflictstyle diff3` //将冲突设置成为3路显示格式,解决编辑冲突最好的办法是使用像kdiff3这样的合并工具

`git mergetool`//启动合并工具

`git show` //检查冲突的文件

对于一些二进制文件，比如图片，这个时候就不能单纯用编辑来解决冲突，这个时候可以采用--ours或者--theirs选项，
或者可以用checkout命令来完全选择则只采用自己的或者别人版本的文件

```
git checkout --theirs imagedir/    //完全使用其他人的图片文件夹下的内容
git add .
git commit
```

`git reset --merge` //将合并撤销，但是要在还没有commit之前

git无法识别内容冲突，内容冲突的时候merge命令还是会生成有效的合并提交，即使合并的版本是正确的，git没有报告任何编辑冲突，该合并也可能是坏的。
（冲突分为编辑冲突和内容冲突，前者指2个开发者对同一个行代码进行了不同的修改，后者是两个开发者对某份代码的几个部分作出各自修改的时候，
一个开发者在修改某一函数的时候，另外一个开发者也同一时间修改了同一函数，a,b两个开发者同时进行修改，但是修改的位置不一样，
此时会把两个修改都进行保存合并，但是这样子的话可能会有内容上的**逻辑问题**，**内容可能是不匹配**。 

避免内容冲突干扰，可以做如下的事情：

> 借助自动化测试构建保护机制
> 
> 使用断言以及前置后置条件
> 
> 定义清晰的接口，实现松耦合
> 
> 静态类型检查


快进合并（**fast forward**）,其实就是一个分支是另外一个分支的祖先这种情况，比如a是祖先，b是后面功能开发的分支，因为a基本没有进行开发，合并的时候就只需要把指针移动到b前面就行，不需要进行分支合并。若干个分支中只有一个分支在持续工作，合并时候只要前移指针就行，不需要产生合并提交，优点可以简化版本库的历史记录使其保持线性发展，缺少点是不能根据已经合并的历史记录来看版本库这一发展。

`git merge --no-ff a_branch` //强制产生一次新的提交

`git log --merges` //查看合并记录,会得到 Merge:ed3b18 ed3b23类似的结果，其中第一个ed3b18是第一父级提交，是合并后HEAD所在的那个提交，代表合并发生地。

`git log --first-parent `//得到第一父级提交历史，也就是一份特性集成的概览,可以看到哪些特性已经被集成，无需再去侦查哪些特性分支上的每一次提交

> 棘手的合并冲突，当两个分支各自演变的轨迹非常不同，有可能会带来棘手的合并冲突，这个时候建议先取消合并，采用逐个解决的思路来来解决这个问题。

首先要收集信息，明确目标分支上当前发生的事情。这里log a..b会有帮助

`git log MERGE_HEAD..HEAD`  //来自于分支HEAD但是不属于MERGE_HEAD的提交，它可以显示出在当前分支上做了哪些事情，而这些事情不会被提交到其他的分支中。

反之亦然，可以通过该符号来显示其他分支上在做而当前分支上没发生的事情
`git log HEAD..MERGE_HEAD`
另外，分支的图形化也很有用
`git log --graph --oneline --decorate HEAD MERGE_HEAD`
`git log --merge` //限制只输出合并提交

`git diff --stat ed3b18 HEAD` //对比发生2次提交的文件数,也可以用difftool来图形化展示提交的变化

> **总之，遇到棘手的合并冲突的时候，应该先收集信息，使用上面那个面的log,diff，如git log MERGE_HEAD..HEAD，知道目前分支上发生的事情，弄清具体的变动以及开发人员再进行合并。**





## 8章 通过变基净化历史

什么情况下会用到rebase命令
> 1.不小心在错误的分支上执行了一次提交，实际上你应该提交到另外的分支
> 
> 2.开发频繁的时候回创建出很多小分支和分岔的历史（钻石链），通过rebase可以将其改造成一部较为平滑的线性历史。

**rebase原理：复制提交**<br>
让我们想要移动的提交序列在目标分支上按照相同的顺序重新再现一遍。“被转移”的这些提交是一些拥有不同提交散列值的新提交。

当前活动分支在feature-a时，进行rebase的步骤

```
git merge master
git rebase master //将活动分支的最新修改纳入master分支
```

before rebase 

```
----  A  -----  B  ----    （master）
       \
        \
         C  ------  D       (feature-a) 
```

after rebase

```

----  A  -----  B 
                 \
                  \
                    C' -----  D' (master）
                    
                    
```

进行变基的过程：
> a.确认涉及到哪些提交，这里的话确认featuer-a上哪些目前不在master分支上，这里就是C,D
> 
> b.确认目标位置，git会确认目标提交的位置，该提交就是master上feature-a将要执行变基的操作的地方（也就是merge操作的汇合处），这里就是B
> 
> c.复制提交，创建提交C',D'
> 
> d.将活动分支重置，将活动分支被移动到上述被复制提交的顶部，也就是D'处。

**<font color="880000">然而很多情况下，我们可能不会直接去调用rebase命令，相反，通常会通过Pull命令加上--rebase选项来对远程版本库中的修改进行变基。</font>**


rebase操作遇到冲突
遇到冲突，需要先手动合并冲突，然后重新添加到暂存区中，再执行`git rebase --continue`操作

`git rebase --continue` //修改冲突之后继续

`git rebase --abort` //取消这次的rebase

`git rebase --skip` //跳过引起冲突的提交

rebase的冲突和合并不同的是rebase被中断的那些提交副本中可能有已经有一部分被执行变基操作。

`git rebase master --onto release1` //现在的活动分支是feature1,原分支是master,通过--onto将活动分支feature1上所有不属于原分支上的所有提交拷贝到指定位置release1分支上。

```
--A--C--D           (master)
   \  \            
    \   E--F       (feature-a)
     G             (release1)

--A--C--D 
   \
    \   E'--F'
     \ /
      G （feature-a从这里开始了）
     
```

执行分支移植的时候操作，切换到待移动的分支上，然后检查所移动的内容，比如要从原来的orgin分支上将the_branch分支移到target分支上，先进行
`git log origin..the_branch`，检查要移动的内容，然后再进行移植

`git rebase origin --onto target`  //命令中的原位置不一定非得是一个分支，也可以是任何提交，比如

`git rebase 742dcf6 --onto target`

注意：分支移植跟rebase也就是变基一样，原件还可以通过散列值进行访问，下一轮垃圾回收过程（通过gc命令）就直接将他们从版本库中删除。
重复（提交的原件和复本存在于同一个版本库中是有问题的，`git log HEAD..a_branch`若存在重复的话，当前分支HEAD可能也已经包含了代码的修改，导致审查和质量检测的复杂性）。
还有就是一旦将某次提交传递给了远程库，那么就不应该使用rebase来移动该提交了。否则其他开发这可能会在其原作上继续他们的工作，这在将来再次合并修改的时候回带来问题。


`git cherry-pick 23ec7089` //cherry-pick操作不会参考历史记录，因此merge和rebase可以被正确的识别成文件的重命名以及移动操作，但是cherry-pick不行，
cherry-pick是创建一次新的提交，为捡取操作，用来将一些小的bug修复传递到不同的发行版本中，或者从即将删除的分支中转移出有用的提交。
根据字面意思很容易理解这个的意思，捡取操作，就是挑有用的东西嘛。

注意：我们应该只对那些还未被传递给其他版本库的提交试用rebase命令，否则这样可能给日后带来非常麻烦的合并冲突。merge后用rebase可以理顺历史。
【书中对cherry-pick的介绍不多，这里有空还要再补充下】


## 9章 版本库之间的交换

几个概念：中央版本库，克隆版本库，主克隆版本库，分叉（fork）

通常情况下，git在创建克隆版本库之后会直接签出工作去，如果不希望如此可以使用 `git clone --bare`来创建一个不带工作区的版本库。
这对于服务器端的版本库是非常有用的，方便开发者可以对其进行直接操作。

`git remote add myCloneName ssh:stachi@server.de:git-book.git` //给远程库起别称

某个版本库被克隆的时候会将原版本库的路径存储为它的源版本库（origin）

`git remote --verbose` //获取可被用于获或推送提交的路径

`git remote rm myCloneName `//删除昵称

`git fetch myClone` //从远程版本库中把提交传递到本地版本库中,没有选定参数的话是拉取所有分支的修改

`git branch -r` //显示远程跟踪分支

`git diff feature_a origin/feature_a `//显示版本库之间分支的不同

**`git log --oneline feature_a..origin/feature_a`** //查看哪些来自远程版本库之间的新增提交,就可以知道远程分支做了什么修改

`git checkout -b feature_b origin/feature_b` //可以从远程版本中签出一个分支，但是此时获取的是一个离线的HEAD状态，不应该这么做，
应该是从远程跟踪分叉(fork)出一个本地分支出来，后面讲到

`git fetch clone feature_b:my_feature_b` //获取clone版本库的feature_b内容，若本地不存在一个my_feature_b的分支，就创建它，已经存在就进行更新

注意：pull = fetch + merge
当然是用pull命令的话如果讨厌钻石链的话，使用
**`git pull --rebase`**

`git push` //将本地库中的提交推送到远程版本库中

`git push clone feature_a` //将feature\_a分支下的新本地提交传给clone所指向的远程版本库，并更新分支指针，使其指向feature_a所在的地方。

注意**push**的几个特点
> 一是要求写访问权限；
> 
> 二是只针对快进合并，这个也是合理的，当远程库有提交的时候，如果此时push成功，可能会导致信息的丢失，所以这个时候要先在当前分支上pull操作再进行push操作，
> 
> 三是无远程跟踪分支；
> 
> 四是无参数调用push，只发送那些在其他版本库中有相同名字匹配的本地分支。而pull和fetch则是选取全部分支。


`git pull orgin feature_a:favorite_feature_a `
//自由命名本地分支，从orgin版本库中引入feature\_a分支，并在本地将该分支命名成为favorite\_feature\_a

`git push origin :favorite_feature_a `//删除远程版本库中的分支，这样可能会导致某些提交丢失，注意冒号左侧留空

`git branch -b favorite_feature_a` //必要情况下还要删除对应的本地库的分支



## 10章 版本标签
可以用tag来标志软件版本
`git tag 1.2.1 master -m "Freshly built"` //创建一个名为1.2.1的标签并加上注释

**`git push origin 1.2.1`** //推送某单一标签，注意推送操作通常不会自动传送标签，但我们明确了某个标签名的时候就可以传送了

`git push --tags` //带--tags参数的push也可以用来推送被传送分支的标签

`git tag` //列出当前所有标签

`git tag -l 1.2* `//通配符

`git show-ref --dereference --tags` //列出标签对象的提交散列值，其中加上--dereference则是打印出其相应提交对象的散列值，它们会被打上^{}符号

`git log --oneline --decorate `//将标签和log一起输出

`git tag --contains f632cd71 `//得到包含该提交的标签,这种情况一般是用于判断某一特定功能或者Bug修复是否被包含在客户所安装的那个版本中，
然后就会把一些列包含该提交的版本显示出来，这时可以通过下面的

**`git log --oneline 1.1.1 | grep "a comment"`** //进一步通过grep的内容进行查找


最好不要修改标签，在便签还没有的传送给其他的版本库的时候可以通过--force参数以重新创建的方式来修改，但是如果便签已经被广泛的运动，
这是再发布改变，势必会引起混淆。标签应该是一个永久性的标志，创建之后就不应该被修改，若真的需要修改，往往只需要一个分支即可。

pull与fetch这两个命令都会自动获取所涉及的分支中的所有标签，除非我们在命令中使用--no-tags参数。

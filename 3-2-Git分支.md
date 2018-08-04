## 2.分支的新建与合并

分支新建与分支合并的场景(工作流)：

* 1、开发某个网站

* 2、为实现某个新的需求，创建一个分支

* 3、在这个分支上开展工作。

此时，你突然接到一个电话说有个很严重的问题需要紧急修补，你将按照如下方式来处理：

* 1、切换到你的线上分支

* 2、为这个紧急任务新建一个分支，并在其中修复它。

* 3、在测试通过之后，切换回线上分支，然后合并这个修补分支，最后将改动推送到线上分支。

* 4、切换回你最初工作的分支上，继续工作。

### 2.1.新建分支

假设存在一个正在开发过程中的项目，并且已经有了一些提交。

图17
一个简单提交历史

如果你需要解决编号为#16的需求问题，你需要创建一个新的分支，然后切换到该分支上。

简写方式：

```
$ git checkout -b iss16
Switched to a new branch 'iss16'

```

非简写方式：

```
$ git branch iss16
$ git checkout iss16
Switched to branch 'iss16'
```

图18
创建一个新分支指针

现在可以开始在iss16分支上解决编号#16的需求，在此过程中，你做了一些提交，所以iss16分支在不断的向前推进，此时HEAD指针指向了iss16分支上。

```
$ git add *
$ git commit -a -m 'new branch'

```

图19
iss16分支随着工作的进展向前推进

当你接到电话时，有个紧急问题等待你来解决，在Git下，不需要把这个紧急问题和iss16的修改混在一起，也不需要还原#16问题的修改，然后再添加关于这个紧急问题的修改，最后将这个修改提交到线上分支。你所需要做的仅仅是切回到master分支。

在切回到master分支之前，必修保证所有的工作目录和暂存区中的修改的文件是被提交的状态。

```
$ git status
On branch iss16
nothing to commit, working directory clean
$ git checkout master
Switched to branch 'master'
```

此时，你的工作目录和在开始处理#16需求之前一模一样，现在可以开始修复紧急问题了。

你可以建立一个针对该紧急问题的分支(hotfix branch)，在该分支上解决该紧急问题。

```
$ git checkout -b hotfix
Switched to a new branch 'hotfix'
$ vim README.md
$ git add *
$ git commit -a -m 'fixed hotfix branch'
[hotfix 1fb7853] fixed the broken email address
 1 file changed, 2 insertions(+)
```


图20
基于master分支的紧急问题分支hotfix branch

注意：当切换分支的时候，Git会重置你的工作目录，使其看起来像回到了你在那个分支上最后一次提交的样子。Git会自动添加、删除、修改文件以确保此时你的工作目录和这个分支最后一次提交时的样子一模一样。

当修改完成这个紧急问题后，使用`git merge`命令，将hotfix分支合并回master分支。

```
$ git checkout master
Switched to branch 'master'
$ git merge hotfix
Updating f42c576..3a0874c
Fast-forward
 index.htmml | 2 ++
 1 file changed, 2 insertions(+)
```


图21
master被快进到hotfix

`Fast-forward`的解释：

Fast-forward表示`"快进"`，由于当前master分支所指向的提交是你当前提交的的直接上游，所以Git只是简单的将指针向前移动。也就是说，当你试图合并两个分支时，如果顺着一个分支走下去能够到达另一个分支，那么Git在合并两者的时候，只会简单的将指针向前推进（指针右移），因为这种情况下的合并操作没有发生冲突。

当解决完该紧急问题之后，你准备继续处理#16的需求，但是你应该先删除hotfix分支，因为已经不再需要它了，master分支已经指向了同一个位置。

简写方式：

```
$ git checkout -d hotfix
Deleted branch hotfix
```

非简写方式：

```
$ git checkout hotfix
$ git delete hotfix
Deleted branch hotfix
```

这时，可以继续处理#16的需求了。

```
$ git checkout iss16
Switched to branch "iss16"
$ vim README.md
$ git add *
$ git commit -a -m 'fixed iss16 branch'
[iss16 ad82d7a] fixed the broken email address
 1 file changed, 2 insertions(+)
```


图22
继续在iss16分支上工作

在hotfix分支上所做的工作并没有包含到iss16分支中。

### 2.2.分支的合并

现在已经完成了#16的需求，需要将iss16分支合并到master分支。

```
$ git checkout master
Switched to branch 'master'
$ git merge iss16
Merge made by the 'recursive' strategy.
README.md | 1 +
1 file changed, 1 insertion(+)
```

此时分支的合并并不是`Fast-forward`的形式，master分支所在提交不是iss16所在提交的直接祖先，
在这种情况下，你的开发历史从一个更早的地方开始分叉开来。Git需要做一些额外的工作。


Git会使用两个分支的末端所指的快照(C4和C5)以及这两个分支的工作祖先(C2)，做一个简单的三方合并。

图23
一次典型合并中所用到的三个快照

Git将三方合并的结果做了一个新的快照并且自动创建一个新的提交指向它，这个被称作一次合并提交

图24
一个合并提交

Git会自行决定选取哪一个提交作为最优的共同祖先，并以此作为合并的基础。

当完成合并之后，需要删除iss16分支。

```
$ git checkout -d iss16
Deleted branch iss16
```

### 2.3.遇到冲突时的分支合并

如果在两个不同的分支中，对同一个文件的同一个部分进行了不同的修改，Git在合并时就会产生冲突：

```
$ git checkout master
Switched to branch 'master'
$ git merge iss16
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed;fix conflicts and then commit the result.
```

Git做了合并，但是没有自动地创建一个新的合并提交。Git会暂停下来，等待你去解决合并产生的冲突。


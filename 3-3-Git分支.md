## 3.分支管理

`git branch`命令在不加任何参数运行时，会得到当前所有分值的一个列表。

```
$ git branch
  iss16
  * master
  feature
```

`*`字符：代表现在正处于哪一个分支(master分支)，也就是当前HEAD指针所指向的分支。如果在这时提交，master分支会随着新的工作向前移动。

`git branch -v`命令：查看每一个分支的最后一次提交。

```
$ git branch -v
 iss16 93b412c new branch
 * master  0f704f0 master
 feature bb5c431 anther brabch
```

`--merged`选项过滤列表中已经合并到当前分支的分支。

```
$ git branch --merged
  iss16
* master
```

在该列表中分支名字前没有`*`号的分支可以使用`git branch -d`命令删除。

`--no-merged`选项过滤列表中尚未合并到当前分支的分支。

```
$ git branch --no-merged
  feature
```

使用`git branch -d`命令删除feature命令时会失败。

```
$ git branch -d feature
error: The branch 'feature' is not fully merged.
If you are sure you want to delete it, run 'git branch -D feature'.
```

可以使用`'git branch -D'`命名删除feature分支，但是会丢掉在该分支上的工作。

## 4.分支开发工作流

### 4.1.长期分支

### 4.2.特性分支


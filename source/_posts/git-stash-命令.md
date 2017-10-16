---
title: git stash 命令
date: 2017-10-06 22:30:51
category: git
tags: git
---
### stash 命令的理解
stash 命令 会将最近一次 commit 提交运用到 工作区 和 暂存区

----------

### git stash apply --index
若在 stash 命令之前 已进行了 git add 操作，git stash pop 操作后，之前暂存的文件却没有重新暂存。  
 想要那样的话，必须使用 --index 选项来运行 git stash apply 命令，来尝试重新应用暂存的修改。

```
$ git stash apply --index
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#      modified:   index.html
#
# Changed but not updated:
#   (use "git add <file>..." to update what will be committed)
#
#      modified:   lib/simplegit.rb
#
```

----------

### git stash --keep-index
它告诉 Git 不要储藏任何你通过 git add 命令已暂存的东西。
```
$ git status -s
M  index.html
 M lib/simplegit.rb

$ git stash --keep-index
Saved working directory and index state WIP on master: 1b65b17 added the index file
HEAD is now at 1b65b17 added the index file

$ git status -s
M  index.html
```

----------

### 针对 untracked 未追踪的文件
##### git stash -u (--include-untracked)
储藏未跟踪文件:  
**默认情况下，git stash 只会储藏已经在索引中的文件**。 如果指定 --include-untracked 或 -u 标记，Git 也会储藏任何创建的未跟踪文件。

```
$ git status -s
M  index.html
 M lib/simplegit.rb
?? new-file.txt

$ git stash -u
Saved working directory and index state WIP on master: 1b65b17 added the index file
HEAD is now at 1b65b17 added the index file

$ git status -s
$
```

##### git stash 命令
未追踪文件在 commit History 区中没有追踪，所以 即便在进行 git stash 之后，未追踪的文件（Untracked files）依然保存在工作区当中。  
```
$ git status -s
 M test.txt
?? untrackk.txt

$ git stash
Saved working directory and index state WIP on master: 5c9d0c4 shanchu
HEAD is now at 5c9d0c4 shanchu

$ git status -s
?? untrackk.txt
```

##### git add 之后, 进行 git stash 命令
如果针对未追踪的文件进行了 git add . 操作后 未追踪文件 成为 新增文件（new file)，在 执行 git stash 命令。则工作目录完全恢复到上一个 commit 状态。在 git stash pop 命令之后，新增文件则是 在工作区当中恢复，**并且在暂存区依然有保留，而不是直接又变成未追踪文件**
```
$ git status -s
 M test.txt
?? untrackk.txt

John@DESKTOP-MHMJ77B MINGW64 ~/Desktop/git stash (master)
$ git add .

John@DESKTOP-MHMJ77B MINGW64 ~/Desktop/git stash (master)
$ git stash
Saved working directory and index state WIP on master: 5c9d0c4 shanchu
HEAD is now at 5c9d0c4 shanchu

John@DESKTOP-MHMJ77B MINGW64 ~/Desktop/git stash (master)
$ git stash pop
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        new file:   untrackk.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   test.txt

Dropped refs/stash@{0} (bb22f9d8c6fc170cbcffbba227a97fb4ae7a4cf3)

John@DESKTOP-MHMJ77B MINGW64 ~/Desktop/git stash (master)
$ git status -s
 M test.txt
A  untrackk.txt

```


![](/images/2017-10-06/git-stash.png)


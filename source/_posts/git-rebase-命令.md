---
title: git rebase 命令
date: 2018-08-27 17:26:21
tags: [git]
---

```
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
```

### pick
使用这个 commit

### reword
使用这个 commit，但在编辑编辑注释前，先对这个commit的注释做独自的编辑

### edit
使用这个 commit, 但是先停下来，可以做其他事情

### squash
1. 使用这个 commit，将这个commit文件提交修改记录合并进如上一个commit中。
2. 在编辑注释信息时，显示当前commit的注释，人为的对几个注释做处理

### fixup
使用这个 commit，如squash一般，但是，在编辑注释信息的时候，不再显示当前commit的注释

### drop
丢弃当前 commit，当前commit所做的文件修改等信息也全部丢失掉

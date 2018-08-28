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

范例
```
* e7e4f4e - step 4 (2 hours ago)
* 9910ab5 - step 3 (2 hours ago)
* e7db7ec - step 2 (2 hours ago)
* 98930ac - step 1 (2 hours ago)
* f65cefd - step 0 (2 hours ago)
```

### pick
使用这个 commit，在 commit history 当中显示这个 commitID

### reword
使用这个 commit(同pick)，但在编辑编辑注释前，先对这个commit的注释做独自的编辑

### edit
使用这个 commit, 但是先停下来，可以做其他事情

### squash
1. 使用这个 commit，将这个commit文件提交修改记录合并进如上一个commit中。在 commit history 当中不再显示这个 commitID
2. 在编辑注释信息时，显示当前commit的注释，人为的对几个注释做处理

### fixup
使用这个 commit，(同squash)，但是，在编辑注释信息的时候，不再显示当前commit的注释

### drop
丢弃当前 commit，当前commit所做的文件修改等信息也全部丢失掉


### 注意事项
`git rebase -i f65cefd` 后，在交互上显示的时候不包含它 `f65cefd commit` 的。
交互的ui 上仅显示 `f65cefd commit` 之后所有的commit。  
之后的这些commit根据用户选择做出不同的合并策略

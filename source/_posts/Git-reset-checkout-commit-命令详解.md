---
title: Git reset checkout commit 命令详解
date: 2017-07-20 17:09:23
category: git
tags: git
---

对 Git 的学习一直处于学了忘，忘了学的状态。主要是第一次学会的时候没有好好进行运用，当想要使用的时，发现，大半都已经忘记了。重新拾起来的时候，又是发现了大量问题。然后再次学习→忘记→学习→忘记，死循环。所以，我还是将自己一直陷进去的坑给填好吧，这样，下次忘记的时候容易→学习→忘记→学习。

### 1、 三个区
看了许多关于 Git 的教程，大多是先说工作目录、暂存目录（索引）和版本库（仓库或者HEAD）。都会配些图片，看来看去，我觉得在[Reset, Checkout, and Revert](https://www.atlassian.com/git/tutorials/resetting-checking-out-and-reverting/)一文中就三个区的概念最为恰当。一图以蔽之![](https://halfmoonvic.github.io/images/git-reset-checkout/1.three_area.svg)  

* Working Directory —— 工作目录  
视力所及处皆是 Working Directory
* Staged Snapshot  
有的将这里称为暂存目录，有的称为索引区。而在[Reset, Checkout, and Revert](https://www.atlassian.com/git/tutorials/resetting-checking-out-and-reverting/)一文中就是简单称之为“快照区”，在下实在是认同。原因以下讲。
* Commit History  
其它文章看到过有将其为版本库或是仓库的，这个让人糊涂。因为 Git 本身就是一个版本控制系统，私以为这三者联合起来才能称之为版本库。而至于 HEAD，说的还是比较贴切的。HEAD 只能在这里游动，跑不了。  

    而将其称为 Commit Histroy 最为贴切处是因为它很好的描述了这个区域的作用 —— 保存 commit 历史。

### 2、commit
初学 Git 时，只是简单的学习 `add commit` 等等基本操作命名，进而去学习各种远程库的推送等等操作，以为这些好复杂。可等到学完的时候，才发现，自己还是不怎么懂最为基本的 `commit` 概念。我自己在学习的过程中逐渐认识到 `commit` 的厉害之处，对它的认识也是一直处于变化当中。到现在根本不在乎它的中文意思应该是什么，而只是简单称它为 `commit` ，动词性的或者名词性质的，统统称之为 `commit`。  

而我就 Git 的个人理解，我认为其思想就是**基于 `commit` 的各种控制操作**。

##### 2.1、 基于 commit 的版本操作控制思想
1. 最开始对 `commit` 的理解就是简单的认为其为中文释义——提交。  
在我们修改了文件之后进行`git add <file_path>`操作，进而进行`git commit <file_path>`。从 `Staged Snapshot` 到 `Commit History` 的操作，这看起来很像一个提交。

2. 继续学习当中，即涉及到第三个区 `Commit History` 中的 `commit` 时，让人沉醉 —— 快照。  
我们可以通过 `checkout` 命名轻松的移动HEAD到不同的 `<commitID>` 上，在上一处对 `commit` 的理解还是一个动词性的（提交）。这里 `commit` 却好像是变成了一个名词——快照（个人跟乐意用`<commitID>`来表示)。

3. 怀疑阶段——我究竟在 提交（`git commit <file_path>`）什么？  
`git commit <file_path>`操作，开始会认为是提交了你在 `Working Dir` 中`add`的文件。这个非常对，没有半点错误。可是，你`commit`操作是 `Staged Snapshot` 与 `Commit History` 两者之间的操作。这个叉跨的太大！  

4. 释疑阶段 —— 合体啊～  
**`commit`既是提交又是快照**  
这里所谓的既是提交又是快照指的仅仅是`git commit <file_path>`一处操作。什么意思？  
我在将文件`git add <file_path>`操作后，我在进行`git commit <file_path>`操作时，事实上是先对`add`进 `Staged Snapshot` 来的文件进行拍照（快照），然后提交给 `Commit History`。  

5. 处处皆是 **commit**  
私以为，快照这个动作是时时进行的。如你在 `Working Dir` 中对文件的`add`操作，是将 `Working Dir` 拍照了一下，传给了 `Staged Snapshot` 了。然后 `Staged Snapshot` 在将传过来的快照传给 `Commit History`。这样，从工作目录开始就是**快照**级别的操作。这些命令操作当中也就不用纠结这个文件到底是怎样的一副状态了。因为它远在你`add`的时候就已经被固定为快照了。

##### 2.2、 根据颜色辨别状态
`git status`时，那个 modified 是显示红色还是绿色，是依据于 commit 所处状态来决定的。  

1. 显示 红色 就是 「Woring Dir 区的 commit」 与 「Staged Snapshot 的 commit / Commit History 的 commit」 有区别。  
红色 modified 即为「Woring Dir 区的 commit」中内容还未被 add。  
2. 显示 绿色 是 「Woring Dir 区的 commit / Staged Snapshot 的 commit」 与 「Commit History 的 commit」 有区别。  
绿色 modified 即为「Staged Snapshot 的 commit」还未被 commit。

印象加深图：
![modified](https://halfmoonvic.github.io/images/git-reset-checkout/2.git-status.jpg)

##### 2.3、 总结 commit
到现在，我已经不在乎 `commit` 到底是什么意思了，只是简单称为 `commit`，如果一定让我翻译一下。我更为乐意称之为「快照」。  
至于那种 `commit` 的传递，我将其认为是 Git 自己内部命令的结果。  
而涉及到「提交」一概念大概只有`git commit <file_path>`中的`commit`。

### 3、reset 命令  
与`checkout`命令相比，我认为`reset`简单的多，但此命令有危险。除非，你知道自己在做什么。

**reset** 许多命令模式会变更 `master`（或者其它分支名，本文只以 `master` 分支举例）分支位置

##### 3.1、 reset 的三种命令模式：

|模式名称|master的位置|暂存区|工作目录|
|:--|:--:|:--:|:--:|
|情况1.1 `--mixed` 无`<file_path>` 操控 `Commit History 和 Staged Snapshot`|修改|修改|不修改|
|情况1.2 `--mixed` 有`<file_path>` 操控 `Staged Snapshot`|不修改|修改|不修改|
|情况 2   `--hard` 操控 `Commit History、Staged Snapshot、Working Directory`|修改|修改|修改|
|情况 3   `--soft` 操控 `Commit History`|修改|不修改|不修改|

示意如下三图，从左至右依次为：mixed(操作对象为HEAD、情况1.1)、hard、soft三种模式。  

* `mixed`模式的时候操作对象即可是 `HEAD` 也可以是 `Staged Snapshop`。  
* `soft hard`模式针对的操作对象均是 `HEAD`。  
如果强行添加`<file_path>`，则会报错`fatal: Cannot do (hard, soft) reset with paths`
* 三种模式操作`HEAD`时，均可以移动`HEAD`及其`HEAD`所处的当前`<branch>`。`mixed`模式带有`<file_path>`时则不可以,其操作控制影响的 是 `Staged Snapshop` 区。

<img src="https://halfmoonvic.github.io/images/git-reset-checkout/mixed.gif" alt="情况1.1" title="--mixed 无 file_path 操控 Commit History 和 Staged Snapshot" style="width: 300px">
<img src="https://halfmoonvic.github.io/images/git-reset-checkout/hard.gif" alt="情况2" title="--hard 操控 Commit History、Staged Snapshot、Working Directory" style="width: 300px">
<img src="https://halfmoonvic.github.io/images/git-reset-checkout/soft.gif" alt="情况3" title="--soft 操控 Commit History" style="width: 300px">

ᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂ 分割线 ᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂᶂ
------------

关于本人所做图片有两处需要阐述，请看下大图：
![高清无码大图！！！](https://halfmoonvic.github.io/images/git-reset-checkout/3.git-reset.jpg)

1. 在 `Working Dir` 与 `Staged Snapshot` 两区，我依然画出了历史 `commit`。不过，未处于激活状态时，是以灰蒙状态般显示的。这个灰蒙状态实际意义上就是不存在的意思，此处是为理解上文中「处处皆 `commit`」之意而做。真正有多个 `commit` 的只有 `Commit History`区。
2. 在我多方多查看参考资料时，发现大多是以箭头形式来指向，如 `Commit History` → `Staged Snapshot`。箭头性的示意图给了我一个误会，那就是 `Commit History` 控制着 `Staged Snapshot` 区，`Staged Snapshot` 区控制着 `Working Dir` 区。  

    而事实上，我更乐意用<font style="color: red;">**同步**</font>一词来做比喻。  

    * `reset hard`的时候，是三区的 `commit` 相同步的； 
    * `reset mixed`的时候则是 `Commit History` 与 `Staged Snapshot` 之间的同步； 
    * `reset soft`就只有 `Commit History` 自己玩耍了。

    可是，光是这样还不够。如`git reset hard HEAD^^`模式的时候，是将`HEAD^^`所在的 `<commitID>`同步至 `Staged Snapshot` 和 `Staged Snapshot` 区。看起来还是用箭头来指示比较合适。  
    这里箭头容易造成的误导是这看起来像是 `Commit History` 在发号施令，故放弃使用箭头形式。那么是谁在发号施令呢？答：是 `reset` 命令（亦或者说是 `reset` 在使三者相同步）。  
    施术者不同，职务不同，采用的形式不同。而我，我更偏向于这种**同步**性的符号，而非箭头这种指示性的符号。

3. 三副动图中，在 `Commit History` 区变化的是 `master` 对不同的 `commit` 的指向。三个区的所依据的同步状态既是 `HEAD` 所处的位置。亦可以说是，HEAD指向哪里，master 跟着到哪里。主导的可以说是通过 `master` 分支 指向 `commit` 的 `HEAD`。

##### 3.2、 情况1.1 情况1.2 `--mixed`模式
当为注明`reset`采用何种模式的时候，默认是采用`--mixed`模式  
此命令常常被用于撤销 `Staged Snapshot` 中的 `add` 进来的文件，一些教程如是说。可事实上，这个命令从始至终都没 `Working Dir` 啥事。  
###### 1. **`git reset <commitID>`**
此种情况会移动`master`位置。   
如下图所示，其作用是将 `Commit History` 区`HEAD^^`处的 `<commitID>` 同步至 `Staged Snapshot` 区。
![](https://halfmoonvic.github.io/images/git-reset-checkout/4.git-reset--commitID.jpg)
###### 2. **`git reset <commitID> <file_path>`**
如下图所示，`HEAD`位置是不变的,自然`HEAD`所指向的分支位置也是不变的。变化的只有 `Staged Snapshot` 区。
![](https://halfmoonvic.github.io/images/git-reset-checkout/5.git-reset-commitID-file_path.jpg)
如此，当你进行的`add`操作时，一般情况下，`Working Dir` 的 `commit` 是会超前于 `Commit History` 区 的 `commit`。  
当进行 `git reset <file_path>`（当未注明 `<commitID>` 时，默认采用 `HEAD`） 操作时，`Staged Snapshot` 区的 `commit` 恢复到上个状态。  
这看起来像是撤销操作，实质上是覆盖操作。即是如上图中的`6`跑到了现在的`7'`位置上

###### 3. 其它无关杂项  
1. 事实上， `HEAD` `<file_path>` 均可省略书写，
    * `git reset <file_path>`  等价于 `git reset HEAD <file_path>`
    * `git reset`              等价于 `git reset HEAD`
2. `git reset <commitID> .` 并不等价于 `git reset <commitID>`  
`git reset HEAD^^ .` 也不会移动 `HEAD`，因为 `.` 就是代表了所有文件。这样，`reset` 的操作对象又变成了 `Staged Snapshot` 区。

##### 3.3 情况2 **hard —— `git reset --hard <commitID>`**  
此命令用于你做错了事情，想要彻底抹除踪迹。  
采用`hard`模式的时候，意味着你想要丢弃在 `Working Dir` 区中的修改。之后继续进行你的工作。详细的 `commit` 步骤不讲了，对照第一条看即可。放张图给你。
![](https://halfmoonvic.github.io/images/git-reset-checkout/6.git-reset-hard.jpg)  

1. `git reset --hard HEAD^^`命令使得 `master` 分支情况发生改变，版本回退了两个，这里就有危险要发生了。  

    假如你已经将最新的`「6commit」`推送到远程库。这个时候，你通过`hard`回退了两个版本，`「5commit」`与`「6commit」`已被在本地 Commit History 区 删除掉（我亦用了灰蒙状态来表示）。这个时候，你再次进行`git commit <file_path>`操作时，新的 commit 将是`「55commit」`。至此，你已与远程库的 `Commit History` 历史相冲突了。反正，我能想到的解决办法只有将远程库删除掉，重新推送本地库，除此之外没有其它招了。

2. `git reset --hard HEAD`命令  
与`git reset --hard HEAD^^`命令相比，`git reset --hard HEAD`命令要柔和的多。如下图所示：  
![](https://halfmoonvic.github.io/images/git-reset-checkout/7.git-reset-hard-head.jpg)  
这个是恢复当前 HEAD 所在的 commit 到以下两个区。也就不存在与远程库相冲突的问题了。

##### 3.4 情况3 **soft —— `git reset --soft HEAD^^`**
soft 仅仅是移动 `Commit History `区的 `master` 就不同的 `commit` 的指向。没什么好说的了。


### 4、checkout 命令
* 情况1.1: **切换`HEAD`到具体分支**「如 `git checkout dev`」
* 情况1.2: **切换`HEAD`到匿名分支**「如 `git checkout <commitID>`」
* 情况2:  **`Staged Snapshop 区 <commitID>`的同步操作，未有分支切换**「如 `git checkout <file_path>`」
* 情况3: **`Commit History 区 <commitID>`同步操作，未有分支切换** 如「`git checkout <commitID> <file_path>`」


##### 4.1、 checkout 作用对象是谁？答：看你写谁
1. 当 `checkout` 用于「情况1.1」，或者「情况1.2」时，那么其作用对象就是 **Commit History 中的 HEAD**。  
其作用是让 `HEAD` 指向某一个 `<commitID>` 并将 此 `<commitID>` 的内容同步至 `Staged Snapshot` 与 `Working Dir`  
2. 而当 checkout 用于「情况2」，其作用对象为当前 **Staged Snapshot 区的 commitID**。  
其作用是让 **当前 `Staged Snapshop`** 区的 `<commitID>` 同步进 **当前 `Woring Dir`**
3. 而当 `checkout` 用于「情况3」，其作用对象为 **Commit History 中 的 `<commitID>`，而非 HEAD**。  
其作用是 同步`Commit History`上某一个`<commitID>`的内容至当下的`Staged Snapshop` 与 `Woring Dir` 区

##### 4.2、 情况1.1 && 1.2  
**HEAD基本上是一直处于指向某一分支的情况的（HEAD指针指向发生过变动），如 HEAD → master、HEAD → dev 亦或是 HEAD → 匿名分支**

1. `git checkout dev`  
我们可以使用诸如`git branch dev`般的命令达到切换分支的效果。这里可以注意一下，所谓的分支切换，**其实质只不过是更改 HEAD 的指向**。切换分支的时候就是在更改 HEAD 指向哪个分支
2. `git checkout <commitID>`  
当我们不写明具体的分支名称的时，而却是写了一个 `Commit History` 中的一个 `<commitID>`。那么，`HEAD` 将被直接切换指向到这个 `<commitID>`。这个时候我们可以称其为**匿名分支**  
修改 bug 的时候会常常这么做吧。  
如下图，此时，你可以通过`git branch fix_bug`命令新建分支，之后在`merge`到主分支。

![](https://halfmoonvic.github.io/images/git-reset-checkout/8.git-checkout-commitID.jpg)

##### 4.3、 情况2: `git checkout <file_path>`  
**HEAD 未发生过变动，一直处于当前分支。**

`git checkout <file_path>`命令常常被用于丢弃本地修改。  
**<span style="color:red">实质上这是一个 Staged Snapshot 区 与 Working Dir 区 两区相同步的一个操作。同步的方向是由 Staged Snapshot 区 → Working Dir 区。</span>**
![](https://halfmoonvic.github.io/images/git-reset-checkout/9.git-checkout-file_path.jpg)

其作用效果与`git reset --hard test.txt`差不多，只是在`git reset --hard test.txt`中，Staged Snapshot 区的 `commit` 被更新了一次，但它和原来一样。

##### 4.4、 情况3: 命令组合`git checkout <commitID> <file_path>`
**HEAD 未发生过变动，一直处于当前分支。**

**<span style="color:red">`git checkout HEAD^^ <file_path>`命令是将 Commit History 中 的 commit 同步到 Staged Snapshot 和 Working Dir 区。而 Commit History 中的 HEAD 并未发生移动。</span>**  

`git checkout HEAD^^ <file_path>`命令造成的结果是 Staged Snapshot 区的 commit 与 Working Dir 区的 commit 保持一致，而与 Commit History 当前的 commit 不同（HEAD未移动的原因）  
如下图，即 4' = 4'' ≠ 6
![](https://halfmoonvic.github.io/images/git-reset-checkout/10.git-checkout-commitID-file_path.jpg)  

同时，我们可以通过 <strong style="color:red">`git checkout HEAD^^ .`</strong> 命令将过去的某一版本下的所有文件放置当前的文件目录下面，而不更改 `HEAD` 指针

##### 4.5 总结
情况1.1，情况1.2，未有指定 `<file_path>` 这时，会发生 `HEAD` 的变动，其指向自然也是由 `Commit History` 指向 `Staged Snapshot` 和 `Woring Dir`    
情况2，情况3 指定了 `<file_path>`时（针对操作是特定文件），如果没有指定 Commit Id，则默认从 `Staged Snapshot` → `Woring Dir`  
如果指定了 `<commitID>`, 则是 在 `<commitID>` → `Staged Snapshot`， `<commitID>` → `Woring Dir`  

### 结语
个人对git的基本命令的理解就是这些。平常使用 Git 过程当中，大致上可以随心所欲的控制各个 commit 版本了。  
以上均是学习过程当中个人总结，如有纰漏之处，请一定指出来啊。。。

### 参考资料：
[图解Git](https://marklodato.github.io/visual-git-guide/index-zh-cn.html)

[細說git reset和git checkout的不同之處](https://medicineyeh.wordpress.com/2015/01/22/%E7%B4%B0%E8%AA%AAgit-reset%E5%92%8Cgit-checkout%E7%9A%84%E4%B8%8D%E5%90%8C%E4%B9%8B%E8%99%95/)

[Reset, Checkout, and Revert](https://www.atlassian.com/git/tutorials/resetting-checking-out-and-reverting)

[Why git can't do hard/soft resets by path?
](https://stackoverflow.com/questions/11200839/why-git-cant-do-hard-soft-resets-by-path)

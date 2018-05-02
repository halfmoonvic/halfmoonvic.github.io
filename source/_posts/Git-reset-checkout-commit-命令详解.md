---
title: Git reset checkout commit 命令详解
date: 2017-07-20 17:09:23
category: git
tags: git
---

对 Git 的学习一直处于学了忘，忘了学的状态。主要是第一次学会的时候没有好好进行运用，当想要使用的时，发现，大半都已经忘记了。重新拾起来的时候，又是发现了大量问题。然后再次学习→忘记→学习→忘记，死循环。所以，我还是将自己一直陷进去的坑给填好吧，这样，下次忘记的时候容易→学习→忘记→学习。

### 1、 三个区
看了许多关于 Git 的教程，大多是先说工作目录、暂存目录（索引）和版本库（仓库或者HEAD）。都会配些图片，看来看去，我觉得在[Reset, Checkout, and Revert](https://www.atlassian.com/git/tutorials/resetting-checking-out-and-reverting/)一文中就三个区的概念最为恰当。一图以蔽之![](/images/git-reset-checkout/01.svg)  

* Working Directory —— 工作目录  
视力所及处皆是 Working Directory
* Staged Snapshot  
有的将这里称为暂存目录，有的称为索引区。而在[Reset, Checkout, and Revert](https://www.atlassian.com/git/tutorials/resetting-checking-out-and-reverting/)一文中就是简单称之为“快照区”，在下实在是认同。原因以下讲。
* Commit History  
其它文章看到过有将其为版本库或是仓库的，这个让人糊涂。因为 Git 本事就是一个版本控制系统，私以为这三者联合起来才能称之为版本库。而至于 HEAD，说的还是比较贴切的。HEAD 只能在这里游动，跑不了。  

    而将其称为 Commit Histroy 最为贴切处是因为它很好的描述了这个区域的作用 —— 保存 commit 历史。

### 2、commit
初学 Git 时，只是简单的学习 add commit 等等基本操作命名，进而去学习各种远程库的推送等等操作，以为这些好复杂。可等到学完的时候，才发现，自己还是不怎么懂最为基本的 commit 概念。我自己在学习的过程中逐渐认识到 commit 的厉害之处，对它的认识也是一直处于变化当中。到现在根本不在乎它的中文意思应该是什么，而只是简单称它为 commit，动词性的或者名词性质的，统统称之为 commit。  

而我就 Git 的个人理解，我认为其思想就是**基于 commit 的各种控制操作**。

##### 2.1、 基于 commit 的版本操作控制思想
1. 最开始对 commit 的理解就是简单的认为其为中文释义——提交。  
在我们修改了文件之后进行`git add <file_path>`操作，进而进行`git commit <file_path>`。从 Staged Snapshot 到 Commit History 的操作，这看起来很像一个提交。

2. 继续学习当中，即涉及到第三个区 Commit History 中对 commit 时，让人沉醉 —— 快照。  
我们可以通过`checkout`命名轻松的移动HEAD到不同的 commit 上，在上一处对 commit 的理解还是一个动词性的（提交）。这里 commit 却好像是变成了一个名词（快照）。

3. 怀疑阶段——我究竟在 提交（`git commit <file_path>`）什么？  
`git commit <file_path>`操作，开始会认为是提交了你在 Working Dir 中`add`的文件。这个非常对，没有半点错误。可是，你`commit`操作是 Staged Snapshot 与 Commit History 两者之间的操作。这个叉跨的太大！  

4. 释义阶段 —— 合体啊～  
**`commit`既是提交又是快照**  
这里所谓的既是提交又是快照指的仅仅是`git commit <file_path>`一处操作。什么意思？  
我在将文件`git add <file_path>`操作后，我在进行`git commit <file_path>`操作时，事实上是先对`add`进 Staged Snapshot 来的文件进行拍照（快照），然后提交给 Commit History。  

5. 处处皆是 **commit**  
私以为，快照这个动作是时时进行的。如你在 Working Dir 中对文件的`add`操作，是将 Working Dir 拍照了一下，传给了 Staged Snapshot 了。然后 Staged Snapshot 在将传过来的快照传给 Commit History。这样，从工作目录开始就是**快照**级别的操作。这些命令操作当中也就不用纠结这个文件到底是怎样的一副状态了。因为它远在你`add`的时候就已经被固定为快照了。

##### 2.2、 根据颜色辨别状态
`git status`时，那个 modified 是显示红色还是绿色，是依据于 commit 所处状态来决定的。  

显示 红色 就是 「Woring Dir 区的 commit」 与 「Staged Snapshot 的 commit / Commit History 的 commit」 有区别。  
显示 绿色 是 「Woring Dir 区的 commit / Staged Snapshot 的 commit」 与 「Commit History 的 commit」 有区别。  
红色 modified 即为「Woring Dir 区的 commit」中内容还未被 add。  
绿色 modified 即为「Staged Snapshot 的 commit」还未被 commit。

印象加深图：
![modified](/images/git-reset-checkout/modified.jpg)

##### 2.3、 总结 commit
到现在，我已经不在乎 commit 到底是什么意思了，只是简单称为 commit，如果一定让我翻译一下。我更为乐意称之为「快照」。  
至于那种 commit 的传递，我将其认为是 Git 自己内部命令的结果。  
而涉及到「提交」一概念大概只有`git commit <file_path>`中的`commit`。

### 3、reset 命令  
与`checkout`命令相比，我认为`reset`简单的多，但此命令有危险。除非，你知道自己在做什么。

**reset** 命令就是会变更master（或者其它分支名，本文只以master分支举例）分支位置的命令，根据具体模式，选择性的变动 Working Dir 的 commit 和 Staged Snapshot 的 commit。  

##### 3.1、 reset 的三种命令模式：

|模式名称|master的位置|索引|工作目录|
|:--:|:--:|:--:|:--:|
|--soft|修改|不修改|不修改|
|--mixed（默认模式）|修改|修改|不修改|
|--hard|修改|修改|修改|

示意如下三图，从左至右依次为：soft、mixed、hard

<img src="/images/git-reset-checkout/soft.gif" style="width: 300px">
<img src="/images/git-reset-checkout/mixed.gif" style="width: 300px">
<img src="/images/git-reset-checkout/hard.gif" style="width: 300px">

关于图片有两处需要阐述，请看下大图：
![高清无码大图！！！](/images/git-reset-checkout/git.jpg)

1. 在 Working Dir 与 Staged Snapshot 两区，我依然画出了历史 commit。不过，未处于激活状态时，是以灰蒙状态般显示的。这个灰蒙状态实际意义上就是不存在的意思，此处是为理解上文中「处处皆 commit」之意而做。真正有多个 commit 的只有 Commit History区。
2. 在我多方多查看参考资料时，发现大多是以箭头形式来指向，如 Commit History → Staged Snapshot。箭头性的示意图给了我一个误会，那就是 Commit History 控制着 Staged Snapshot 区，Staged Snapshot 区控制着 Working Dir区。  

    而事实上，我更乐意用<font style="color: red;">**同步**</font>一词来做比喻。  

    * `reset hard`的时候，是三区的 commit 相同步的； 
    * `reset mixed`的时候则是 Commit History 与 Staged Snapshot之间的同步； 
    * `reset soft`就只有 Commit History 自己玩耍了。

    可是，光是这样还不够。如`reset hard HEAD^^`模式的时候，是将`HEAD^^`所在的commit同步至 Staged Snapshot 和 Staged Snapshot 区。看起来还是用箭头来指示比较合适。  
    这里箭头容易造成的误导是这看起来像是 Commit History 在发号施令，故放弃使用箭头形式。那么是谁在发号施令呢？答：是`reset`命令（亦或者说是 reset 在使三者相同步）。  
    施术者不同，职务不同，采用的形式不同。而我，我更偏向于这种**同步**性的符号，而非箭头这种指示性的符号。

3. 三副动图中，在 Commit History 区变化的是 master 对不同的 Commit 的指向。三个区的所依据的同步状态既是 HEAD 所处的位置。亦可以说是，HEAD指向哪里，master 跟着到哪里。主导的可以说是通过 master 分支 指向 commit 的 HEAD。

##### 3.2、 reset 命令详解
1. **mixed —— `git reset -- <file_path>`**  
此命令常常被用于撤销 Staged Snapshot 中的 add 进来的文件，一些教程如是说。可事实上，这个命令从始至终都没 Working Dir 啥事。  

    * 当未注明`reset`采用何种模式时，默认将会采用`mixed`模式。可以看上文所述，当采用`mixed`的时候，只是 Commit History 与 Staged Snapshot 两区在同步。
    * 当未注明 HEAD 所处 Commit 位置时，默认采用 HEAD。  
    所以`git reset -- <file_path>`等同于`git reset HEAD -- <file_path>`。
    * 当未注明 `<file_path>` 时默认是操作所有文件。  
    所以 `git reset HEAD` 等同于 `git reset HEAD <all file_path>`。  
    事实上 `git reset` 也同于 `git reset HEAD`(2018/05/02补记)
    * 结合以上两点，`git reset -- <file_path>`命令实际上是让 Commit History 处的 Commit 同步至 Staged Snapshot 区。  
    如此，当你进行的`add`操作时，一般情况下，Working Dir 的 commit 是会超前于 Commit History 区 的 commit，再次进行 `git reset -- <file_path>` 操作，Staged Snapshot 区的 commit 恢复到上个状态，这看起来像是撤销操作，其实并不。  
    如下图所示，`git reset -- <file_path>`命令只是将 Staged Snapshot 区的红色 commit 删掉，利用`mixed`模式的同步状态，使 Staged Snapshot 区的 commit 与 Commit History 区的 commit 相一致。（注：Commit History 区到底是处于哪个 commit 状态是由 HEAD 所在位置来决定的，下文讲。）

    如下图所示：
    ![](/images/git-reset-checkout/git-reset-HEAD.jpg)

2. **hard —— `git reset --hard HEAD^^`**  
此命令用于你做错了事情，想要彻底抹除踪迹。  
采用`hard`模式的时候，意味着你想要丢弃在 Working Dir 区中的修改。之后继续进行你的工作。详细的 commit 步骤不讲了，对照第一条看即可。放张图给你。
![](/images/git-reset-checkout/git-reset-hard.jpg)  

    1. `git reset --hard HEAD^^`命令使得 master 分支情况发生改变，版本回退了两个，这里就有危险要发生了。  
    
        假如你已经将最新的「6commit」推送到远程库。这个时候，你通过`hard`回退了两个版本，「5commit」与「6commit」已被在本地 Commit History 区 删除掉（我亦用了灰蒙状态来表示）。这个时候，你再次进行`git commit <file_path>`操作时，新的 commit 将是「55commit」。至此，你已与远程库的 commit 历史相冲突了。反正，我能想到的解决办法只有将远程库删除掉，重新推送本地库，除此之外没有其它招了。

    2. `git reset --hard HEAD`命令  
    与`git reset --hard HEAD^^`命令相比，`git reset --hard HEAD`命令要柔和的多。如下图所示：  
    ![](/images/git-reset-checkout/git-reset-hard-head.jpg)  
    这个是恢复当前 HEAD 所在的 commit 到以下两个区。也就不存在与远程库相冲突的问题了。
    3. `git reset (--hard, --soft) 3857c85 note.md`  
    hard 与 soft 模式不允许针对指定文件的操作
    fatal: Cannot do (hard, soft) reset with paths.

3. soft 模式  
soft 仅仅是移动 Commit History 区的 MASTER 就不同的 commit 的指向。没什么好说的了。

### 4、checkout 命令
* 情况1.1: **切换分支**「如 `git checkout dev`」
* 情况1.2: **将 Commit History 区 commit 同步至 Staged Snapshot 区 和 Working Dir**「如 `git checkout HEAD^^`」
* 情况2: **将 Staged Snapshot 区 commit 同步至 Woring Dir**「如 `git checkout <file_path>`」
* 情况3: 神奇的命令组合「**`git checkout HEAD^^ <file_path>`**」

##### 4.1、 checkout 作用对象是谁？答：看你写谁
当 checkout 用于「情况1.1」，或者「情况1.2」时，那么其作用对象就是 **Commit History 中的 HEAD**。  
而当 checkout 用于「情况2」，其作用对象为当前 **Staged Snapshot 区的 commit**。  
当 checkout 用于「情况3」时，其作用对象为 **Commit History 中 的 commit，而非 HEAD**。

##### 4.2、 情况1.1 && 1.2  
**HEAD基本上是一直处于指向某一分支的情况的（HEAD指针指向发生过变动），如 HEAD → master、HEAD → dev。**

1. `git checkout dev`  
我们可以使用诸如`git branch dev`般的命令达到切换分支的效果。这里可以注意一下，所谓的分支切换，**其实质只不过是更改 HEAD 的指向**。切换分支的时候就是在更改 HEAD 指向哪个分支
2. `git checkout commitID`  
当我们不写明具体的分支名称的时，而却是写了一个 Commit History 中的一个 commit。那么，HEAD 将被直接切换指向到这个 commit。这个时候我们可以称其为**匿名分支**  
修改 bug 的时候会常常这么做吧。  
如下图，此时，你可以通过`git branch fix_bug`命令新建分支，之后在`merge`到主分支。

![](/images/git-reset-checkout/git-ck-head^^.jpg)

##### 4.3、 情况2: `git checkout <file_path>`  
**HEAD 未发生过变动，一直处于当前分支。**

`git checkout <file_path>`命令常常被用于丢弃本地修改。  
**<span style="color:red">实质上这是一个 Staged Snapshot 区 与 Working Dir 区 两区相同步的一个操作。同步的方向是由 Staged Snapshot 区 → Working Dir 区。</span>**
![](/images/git-reset-checkout/git-ck-testtxt.jpg)

其作用效果与`git reset --hard test.txt`差不多，只是在`git reset --hard test.txt`中，Staged Snapshot 区的 commit 被更新了一次，但它和原来一样。

##### 4.4、 情况3: 命令组合`git checkout HEAD^^ <file_path>`
**HEAD 未发生过变动，一直处于当前分支。**

**<span style="color:red">`git checkout HEAD^^ <file_path>`命令是将 Commit History 中 的 commit 同步到 Staged Snapshot 和 Working Dir 区。而 Commit History 中的 HEAD 并未发生移动。</span>**  

`git checkout HEAD^^ <file_path>`命令造成的结果是 Staged Snapshot 区的 commit 与 Working Dir 区的 commit 保持一致，而与 Commit History 当前的 commit 不同（HEAD未移动的原因）  
如下图，即 4' = 4'' ≠ 6
![](/images/git-reset-checkout/git-ck-head^^-testtxt.jpg)  

同时，我们可以通过 <strong style="color:red">`git checkout HEAD^^ .`</strong> 命令将过去的某一版本下的所有文件放置当前的文件目录下面，而不更改 HEAD 指针

##### 4.5 总结
情况1.1，情况1.2，未有指定 `<file_path>` 这时，会发生 HEAD 的变动，其指向自然也是由 Commit History 指向 Staged Snapshot 和 Woring Dir    
情况2，情况3 指定了 `<file_path>`时（针对操作是单个文件），如果没有指定 Commit Id，则默认从 Staged Snapshot → Woring Dir  
如果指定了 Commit Id, 则是 在 Commit Id → Staged Snapshot， Commit Id → Woring Dir  

### 结语
个人对git的基本命令的理解就是这些。平常使用 Git 过程当中，大致上可以随心所欲的控制各个 commit 版本了。  
以上均是学习过程当中个人总结，如有纰漏之处，请一定指出来啊。。。

### 参考资料：
[图解Git](https://marklodato.github.io/visual-git-guide/index-zh-cn.html)

[細說git reset和git checkout的不同之處](https://medicineyeh.wordpress.com/2015/01/22/%E7%B4%B0%E8%AA%AAgit-reset%E5%92%8Cgit-checkout%E7%9A%84%E4%B8%8D%E5%90%8C%E4%B9%8B%E8%99%95/)

[Reset, Checkout, and Revert](https://www.atlassian.com/git/tutorials/resetting-checking-out-and-reverting)

[Why git can't do hard/soft resets by path?
](https://stackoverflow.com/questions/11200839/why-git-cant-do-hard-soft-resets-by-path)

---
title: Redux Vs Vuex 学习与比较
date: 2017-11-27 22:39:57
category: react
tags: [vuex redux]
---
1. vuex 的 mutations 选项 更像是 redux 当中 reducer，都是一些注册函数。待用户派发一些事件之后，执行程序输出新的 state
2. vue 并没有显示的定义 那些`{type, payload}`。 而在触发mutation的时候，是通过提交{type:事件类型} 这种方式已经指定好了相应的注册函数（mutations)。  
而 redux 则是 先确定好 type，在通过 判断 type 来执行相应的 注册函数（reducer）
3. 相比vuex ，redux多了创建 action这么一步

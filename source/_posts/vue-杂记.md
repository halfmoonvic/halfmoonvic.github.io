---
title: vue 杂记
date: 2017-12-06 11:39:20
tags: vue
---

###### 1. 检测变化的注意事项
受现代 JavaScript 的限制 (以及废弃 Object.observe)，Vue 不能检测到对象属性的添加或删除。由于 Vue 会在初始化实例时对属性执行 getter/setter 转化过程，所以属性必须在 data 对象上存在才能让 Vue 转换它，这样才能让它是响应的。  

注意： 针对对象的数据变化，要在data当中提前设置好其各个属性值

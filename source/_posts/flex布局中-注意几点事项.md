---
title: flex布局中 注意几点事项
date: 2018-05-27 16:02:37
category: css
tags: [css, 布局]
---
flex布局中 注意几点事项  

* 对于同一组的单元中 固定单元宽度设置的时候，设置其 max-width。不然，内部元素过宽的时候会将其撑开。 也不要设置 width 为多少，火狐浏览器不兼容 
```
flex: 0 0 402px;
max-width: 402px;
```

* 两个单元按比例布局。 `flex: 1 1 1px && flex: 3 3 3px` (对于火狐浏览器问题)
两个单元固定比例宽高度缩放的时候，如果其内部元素过宽过高会超出单元的宽高度。
这个时候 设置 max-width max-height 也没有意义了。直接设置 oveflow 属性，，可避免超出
```
overflow-x: hidden;
overflow-y: auto;
```

* `flex: 1 vs flex: auto` (对于火狐浏览器问题)
针对火狐还有一点，就是不要设置其 flex: 1(flex-basic: 0%). 而设置其 flex: auto(flex-basic: auto). 不然其内容还是会撑开父元素

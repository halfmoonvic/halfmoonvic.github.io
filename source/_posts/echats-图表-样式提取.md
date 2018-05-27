---
title: echats 图表 样式提取
date: 2018-05-27 16:04:37
category: js
tags: [Echarts, 可视化]
---
### echats 图表注意事项：
##### 1. 柱状图 统一 柱子宽度： 16
```
  barWidth: 'auto',
  barMaxWidth: 16,
```


##### 2. x 轴 y 轴 字体大小  x轴 刻度标签与轴线之间的距离。 (*已放置到echats－style.js*)
```
axisLabel: {
  textStyle: {
    color: '#fff',
    fontSize: 20,
    fontWeight: 'lighter'
  },
  margin: 18,
}
```

##### 3. x 轴 刻度颜色设置
```
axisTick: {
  show: true
},
axisLine: {
  lineStyle: {
    color: '#036ca8'
  }
}
```

##### 4. y 轴 单位的大小 位置
```
nameTextStyle: {
  fontSize: 18,
  color: '#fff',
  padding: [0, 40, 15, 0]
}
```

##### 6. 图例 legend 的文字样式    (*已放置到echats－style.js*)
```
textStyle: {
  color: '#fff',
  fontSize: 18
}
```

##### 7. 标签在坐标轴刻度线中间
```
boundaryGap: true,
axisTick: {
  alignWithLabel: false,
},
```

##### 8. 有 markearea 的echats图表，防止 tootip 有两个 背影出现 解决方案：(应该有多种方案)
1. `tooltip: {trigger: 'item', }`


##### 9. 折线图上的圆点样式设置
```
symbol: 'circle',
symbolSize: 15,
itemStyle: {
  normal: {
    color: '#00B874',  // 定义 线条及圆点内部的颜色
    borderColor: '#FEFB00', // 定义圆点边框颜色
  },
}
```

##### 10. tootip 中对于 折线图的 响应，为轴线，而非阴影。将shadow去掉
```
tooltip: {
  trigger: 'axis',
  axisPointer: {
    type: 'line',             // 样式 为 line 
    lineStyle: {
      color: 'rgb(216, 115, 24)',
      type: 'dashed',
      width: 2
    }          
}
```

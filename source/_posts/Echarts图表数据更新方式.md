---
title: Echarts图表数据更新方式
date: 2017-08-29 09:39:22
category: js
tags: [Echarts, 可视化]
---
### echarts 图表数据更新方式
##### 问题表现
页面 echarts 图表 在做一些数据交互的时候会涉及到一些数据的更新。如果只是简单的新数据传递给echarts图表，许多时候我们会发现 旧有的时候依然残留在页面之上
如下一示例  
旧数据
```
series: [{
    name: 'A',
    type: 'bar',
    data: [200, 300, 100, 330, 350],
},{
    name: 'B',
    type: 'bar',
    data: [500, 100, 400, 300, 600],
}]
```
新数据
```
series: [{
    name: 'C',
    type: 'bar',
    data: [200, 300, 100, 330, 350],
}]
```
如果不做处理，只是进行 `chart.setOption(newData)`,那么旧有的数据 B 将依旧残留在页面之上  

[数据残留 实例](https://jsfiddle.net/halfmoon/wscgnxy5/1/)
<script async src="//jsfiddle.net/halfmoon/fqfto7j4/7/embed/result,js,html/"></script>

---------

##### 问题产生原因
```
chart.setOption(option, {
    notMerge: ...,
    lazyUpdate: ...,
    silent: ...
})
```
**`setOption` 中的 `notMerge` 即为不合并之意默认为 `false`，即合并。**  
我们在 `setOption` 的时候 仅仅是传输了一些 新的数据，其它的一些如 图表的类型，基本样式，tooltip 等等均为再次设置，这个时候 `notMerge` 便起作用了，它将 `echarts` 一些原有的一些数据样式等与 我们的 newData 一同合并进入 chart 实例当中，从而显现完整的 echarts 图表。  
问题是，我们很希望 只是一些样式 进行了合并，而数据不需要了。这时候矛盾点产生了。

---------

##### 解决途径
弃用 默认 `merge`，主动加载完整的 echarts 图表配置。  
在我们每次 进行 `setOption` 的时候，如此操作 `demo.setOption(demoOption, true)` 即可。   
这样就需要每次 传入 完整的 echarts 图表设置，像是一些字体大小颜色等等等。我们关注的核心只应是 数据层面，及其一些跟随数据变化的文字样式等。  
###### 解决数据重复拼写问题 **`$.extend(true, demoOption, newOption)`**

可以通过 jquery的深拷贝方法，将我们 的数据及一些样式更改 放在 `newOption` 当中，`demoOption` 中仅仅是一些 `echarts` 实例的一些基本配置。如此，  

1. 可以实现对 数据及样式的集中管理
2. 免去了数据 合并 造成旧数据残留的问题

基本配置如下
```
$(function () {
    var echartOpt = {
        fz: '15',
        color: '#fff'
    };

    var demo = echarts.init(document.getElementById('trade-history'));
    function Initdemo(data, obj) {
        var demoOption = echarts 图表基本样式

        // 有时候 我们也需要将 数据 的样式 也一同提炼出来
        var seriesStyle =  [{
                itemStyle: {
                    normal: {
                        barBorderRadius: 10,
                        borderColor: '#3fa7dc',
                        color: 'red'
                    }
                }
            },
            ...{othersStyle}]                          

        var legend =  ['A', 'a']         // 新的图例数据
        var newOption = {
            legend: {
                data: legend
            },
            series: data                // 传入的数据
        }

        newOption.series.forEach(function (item, index) {
            $.extend(true, item, seriesStyle[index])    // 将数据样式 加载到我们的 新数据上面
        });

        $.extend(true, demoOption, newOption)  // 新旧数据合并

        demo.setOption(demoOption, true);
    }
    Initdemo();
})

```

### 实例演示
[柱状图 曲线图](https://jsfiddle.net/halfmoon/kL5fru4e/15/)  
<script async src="//jsfiddle.net/halfmoon/mdwmdex9/6/embed/result,js,html/"></script>

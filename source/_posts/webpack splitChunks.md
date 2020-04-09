---
title: webpack splitChunks
date: 2020-03-27 15:15:20
tags: webpack
---

### 一、 webpack 代码分割方式：
1. 入口起点：使用 entry 配置手动地分离代码。
2. 动态导入：通过模块的内联函数调用来分离代码。 import(path)
3. 防止重复：使用 splitChunks 去重和分离 chunk

### 二、 splitChunks
`splitChunks` 拆分 `chunk` 的那些规则的逻辑是 **`且`**  

```
   ≡ app.js    import('./a')  import('./b') 
   ≡ a.js      import Vue from 'vue'
   ≡ b.js      import Vue from 'vue'
```

如果配置 `splitChunks: false`，那么 `Vue` 或被打包两份。  
这就是继续拆分 chunk 的必要性！

![bundle double](/images/2020-03-27/bundle-double.png)


```
Built at: 03/27/2020 2:27:15 PM
     Asset       Size  Chunks             Chunk Names
      1.js     68 KiB       1  [emitted]
      2.js     68 KiB       2  [emitted]
    app.js   2.11 KiB       0  [emitted]  app
index.html  199 bytes          [emitted]
Entrypoint app = app.js
[0] ./src/app.js 110 bytes {0} [built]
[1] ./src/a.js 85 bytes {1} [built]
[2] ./src/b.js 103 bytes {2} [built]
[3] (webpack)/buildin/global.js 472 bytes {1} {2} [built]
    + 4 hidden modules
```

### 二、 **`chunks: "async initial all"`**
This indicates which chunks will be selected for optimization. When a string is provided, valid values are all, async, and initial. Providing all can be particularly powerful, because it means that chunks can be shared even between async and non-async chunks  

个人理解：This indicates which chunks will be selected for optimization —— 选取哪些 chunk 进行优化。  
而已有的 chunk 来源仅两点： 1. 入口文件 2. 动态加载

#### 1) `chunks: initial` 针对对象为入口文件
初始块：设想一下，webpack 中所有资源都是可以被引用的。这里仅包含一个入口文件，即在入口文件被 fetch 的时候，入口文件所引用的所有其它文件也跟随着被 fetch。  
`chunks: initial` 便针对此种情况而设置的吧。即在入口文件中，如果有些包体积过大之类的就将其 拆分为 单独的chunk。
```
   ≡ app.js    import('./a')  import('./b')  import Vue from 'vue'
   ≡ a.js      import Vue from 'vue'
   ≡ b.js      import Vue from 'vue'
```
![bundle initial](/images/2020-03-27/bundle-initial.png)
```
         Asset       Size  Chunks             Chunk Names
          2.js  111 bytes       2  [emitted]
          3.js  141 bytes       3  [emitted]
        app.js   2.35 KiB       0  [emitted]  app
    index.html  237 bytes          [emitted]
vendors~app.js     68 KiB       1  [emitted]  vendors~app
Entrypoint app = vendors~app.js app.js
```

1. `chunks: initial` 导致 入口文件会被 splitChunks
2. 入口文件 `app.js` 中 引入了两个 动态包 `a.js` `b.js` 会被拆分成两个 chunk
3. 入口文件 `app.js` 中 引入了 `vue`，因为 `vue` 过大，其被拆分成了一个 单独的 chunk，打包进 `vendors~app.js` 中
4. `a.js` 与 `b.js` 当中虽然引用了 `vue`，但 `vue` 不会在被打包进这两个 chunk 中，因为 `vue` 已经被打包成了单独的 chunk了，直接引用即可。
5. 如果说，入口文件 `app.js` 未 引用 `vue`，那么其打包结果与`splitChunks: false`一样  

另外，对于有多个入口文件的时候，依然起作用  
比如  
```
   ≡ app.js        import Vue from 'vue'
   ≡ login.js      import Vue from 'vue'
```
![bundle multi entry](/images/2020-03-27/bundle-multi_entry.png)
```
               Asset       Size  Chunks             Chunk Names
              app.js   1.55 KiB       1  [emitted]  app
          index.html  275 bytes          [emitted]
            login.js   1.55 KiB       2  [emitted]  login
vendors~app~login.js     68 KiB       0  [emitted]  vendors~app~login
Entrypoint login = vendors~app~login.js login.js
Entrypoint app = vendors~app~login.js app.js
```

#### 2) `chunks: async` 针对对象为 动态加载的文件
`chunks: async` 针对的则是就 异步引用的块中所引用的包 的拆分。拆分规则也是 诸如包的体积 大小，引用次数这些。
即 `initial` 与 `async` 的不同在针对拆分对象的不同。  
前者就是 入口文件中 引用的所有静态包  
后者则是 动态组件 引用的所有的静态包（当遇到动态引用的就在拆分出一个chunk来，转过头来去分析这个动态包所引用的静态包，对静态包进行拆分，就是一个递归的一个过程，自己理解）

```
   ≡ app.js    import('./a')  import('./b')
   ≡ a.js      import Vue from 'vue'
   ≡ b.js      import Vue from 'vue'
```

![bundle initial](/images/2020-03-27/bundle-async.png)
```
Built at: 03/27/2020 3:05:56 PM
     Asset       Size  Chunks             Chunk Names
      0.js     68 KiB       0  [emitted]
      2.js  110 bytes       2  [emitted]
      3.js  141 bytes       3  [emitted]
    app.js   2.16 KiB       1  [emitted]  app
index.html  199 bytes          [emitted]
Entrypoint app = app.js
[0] ./src/app.js 110 bytes {1} [built]
[1] ./src/a.js 86 bytes {2} [built]
[2] ./src/b.js 103 bytes {3} [built]
[3] (webpack)/buildin/global.js 472 bytes {0} [built]
    + 4 hidden modules
```

1. `chunks: asnc` 导致 动态加载的文件会被 splitChunks
2. 入口文件 `app.js` 中 引入了两个 动态包 `a.js` `b.js` 会被拆分成两个 chunk
3. `a.js` 引入的 `vue` 过大，其被拆分成了 单独的 chunk，打包进 `0.js` 文件中
4. `b.js` 顺势直接引用 被拆分出来的 `vue` chunk

### 三、 webpack 打包优化
首先讲，我的理解是 打包优化是一个伪命题，对于打包，我更想使用的一个词语是 —— 平衡  

#### 1) 先说下这个问题的来源
  以首屏加载为例

  1. 如果所有代码打包到一个 js 文件当中，造成的结果是
      * 首屏加载了诸多首屏不需要的 js 代码，资源浪费
      * 一个 js 文件体积过大，页面需要需要等待这个 js 资源，造成页面长久的卡死状态
  2. 如果尽可能的拆分 js 到不同的文件
      * 优点：遗弃了不需要的 js 加载
      * 缺点：拆分的越多 浏览器发起的请求数量越多，等待全部js 资源发起并返回可能也会花销一段时间
      * [Browser connection limitations](https://docs.pushtechnology.com/cloud/latest/manual/html/designguide/solution/support/connection_limitations.html), 总结就是：ie 不同版本 限制数量是不一样的，其它浏览器都是 6个

#### 2) 解决方案
  1. 从代码体积上进行拆分
      * 减少非当前页面需要的 js 请求
      * 拆分出其它chunk 需要的公共代码，形成缓存
  2. 从文件数量上尽可能减少
      * 合并 较小体积的 公共代码

----------

```
splitChunks: {
    // 表示选择哪些 chunks 进行分割，可选值有：async，initial和all
    chunks: "async",
    // 表示新分离出的chunk必须大于等于minSize，默认为30000，约30kb。
    minSize: 30000,
    // 表示一个模块至少应被minChunks个chunk所包含才能分割。默认为1。
    minChunks: 1,
    // 表示按需加载文件时，并行请求的最大数目。默认为5。
    maxAsyncRequests: 5,
    // 表示加载入口文件时，并行请求的最大数目。默认为3。
    maxInitialRequests: 3,
    // 表示拆分出的chunk的名称连接符。默认为~。如chunk~vendors.js
    automaticNameDelimiter: '~',
    // 设置chunk的文件名。默认为true。当为true时，splitChunks基于chunk和cacheGroups的key自动命名。
    name: true,
    // cacheGroups 下可以可以配置多个组，每个组根据test设置条件，符合test条件的模块，就分配到该组。模块可以被多个组引用，但最终会根据priority来决定打包到哪个组中。默认将所有来自 node_modules目录的模块打包至vendors组，将两个以上的chunk所共享的模块打包至default组。
    cacheGroups: {
        vendors: {
            test: /[\\/]node_modules[\\/]/,
            priority: -10
        },
        // 
    default: {
            minChunks: 2,
            priority: -20,
            reuseExistingChunk: true
        }
    }
}
```



```
// webpack.config.js
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

module.exports = {
  entry: { app: "./src/app.js" },
  output: {
    filename: "[name].js",
    path: path.resolve(__dirname, "dist")
  },
  optimization: {
    splitChunks: {
      chunks: "async",
      minChunks: 1
    }
  },
  plugins: [
    new HtmlWebpackPlugin(),
    new BundleAnalyzerPlugin()
  ]
};
```


```
// a.js 
import Vue from 'vue';

new Vue();

function A(filename) {
  console.log(filename);
}
```

```
// b.js
import Vue from 'vue';

new Vue();

function b(filename) {
  console.log(filename)
}
b('b.js')
```

```
// index.js
import('./a')
import('./b')

function index(filename) {
  console.log(filename)
}
index('app.js')

```

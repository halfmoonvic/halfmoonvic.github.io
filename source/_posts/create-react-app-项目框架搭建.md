---
title: create-react-app 项目框架搭建
date: 2017-12-15 14:53:09
category: 框架
tags: [react]
---

#### 一、 创建项目
1. `create-react-app 项目名称`
2. `npm run eject`

#### 二、 别名配置
###### 1. 添加函数
```
function resolveDir (dir) {
  return path.join(__dirname, '..', dir)
}
```
###### 2. resolve.alias 下进行别名配置(自行进行追加操作)
```
alias: {
  '@': resolveDir('src'),
  'component': resolveDir('src/component'),
  'container': resolveDir('src/container'),
  'store': resolveDir('src/store'),
  'common': resolveDir('src/common'),
  'api': resolveDir('src/api'),
},
```

#### 三、 装饰器 @ 支持
###### 1. 安装 `babel-plugin-transform-decorators-legacy`
###### 2. 修改babel处plugins配置
```
"babel": {
  "presets": [
    "react-app"
  ],
  "plugins": [
    "transform-decorators-legacy"
  ]
},
```

#### 四、 redux支持
###### 1. 安装包 `react-redux redux redux-thunk`
###### 2. 文件目录结构
```
▾ src\
   ▸ api\
   ▸ common\
   ▾ store\
      ≡ action-types.js         // 常量文件，存放 action-type
      ≡ actions.js              // 存放普通的 action-creator
      ≡ async-actions.js        // 存放 含有异步的 action-creator, 其返回值是一个 action-creator
      ≡ index.js                // 输出 store
      ≡ reducers.js             // 存放所有的 reducer
      ≡ states.js               // 存放所有的 initState 值
   ≡ App.js
```

#### 五、 react-router-dom
安装 `react-router-dom`

#### 六、 scss 语法支持
###### 1. 安装 `node-sass sass-loader` 两个包
###### 2. 修改 `webpack.config.dev.js webpack.config.prod.js` 下面的 rules 配置
下面的配置修改放置到 oneOf 数组下面
```
{
  test: /\.scss$/,
  use: [{
      loader: "style-loader" // 将 JS 字符串生成为 style 节点
  }, {
      loader: "css-loader", // 将 CSS 转化成 CommonJS 模块
      options: {
        sourceMap: true
      }
  }, {
      loader: "sass-loader", // 将 Sass 编译成 CSS
      options: {
        sourceMap: true
      }
  }]
},
```

#### 七、 antd 库按需引入
###### 1. 安装 `antd-mobile babel-plugin-import`
###### 2. 按需引入配置 修改babel处plugins配置
```
"babel": {
  "presets": [
    "react-app"
  ],
  "plugins": [
    [
      "import", {
        "libraryName": "antd-mobile",
        "style": "css"
      }
    ],
  ]
},
```

#### 八、 其它依赖包安装
1. `prop-types`
2. `axios`
3. `qs`

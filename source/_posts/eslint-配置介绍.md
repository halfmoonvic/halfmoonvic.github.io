---
title: eslint 配置介绍
date: 2018-08-05 20:50:13
tags: api
---

### parser —— 解析器
[浅入浅出eslint 与实作](https://denny.qollie.com/2016/07/11/eslint-fxcking-setup/)  
parser 就是将我们语法的理解给「程式化」成一个树状的结构
```
parser: 'babel-eslint',
```

### parserOptions —— 解析器选选项
1. ecmaVersion - 默认设置为5（默认），你可以使用 6、7、8 或 9 来指定你想要使用的 ECMAScript 版本。你也可以用使用年份命名的版本号指定为 2015（同 6），2016（同 7），或 2017（同 8）或 2018  
2. sourceType - 设置为 "script" (默认) 或 "module"（如果你的代码是 ECMAScript 模块)。
3. ecmaFeatures - 这是个对象，表示你想使用的额外的语言特性
    * globalReturn - 允许在全局作用域下使用 return 语句
    * impliedStrict - 启用全局 strict mode (如果 ecmaVersion 是 5 或更高)
    * jsx - 启用 JSX
```
parserOptions: {
  ecmaVersion: 6,
  sourceType: 'module'
},
```

### env 运行环境
An environment defines global variables that are predefined.  
指定脚本的运行环境。每种环境都有一组特定的预定义全局变量。
```
"env": {
    "browser": true,
    "node": true,
    "es6":true,
    commonjs: true
    "mocha":"true",
    "qunit":true,
    "jquery":true,
    "mongo":true,
    //通过插件名命名空间引入插件中的环境
    "example/custom": true
},
```

### 二、 globals —— 全局变量
脚本在执行期间访问的额外的全局变量。
```
globals: {
  __DEV__: true,
  __WECHAT__: true,
  __ALIPAY__: true,
  App: true,
  Page: true,
  Component: true,
  Behavior: true,
  wx: true,
  getApp: true
},
```

### plugins —— 第三方插件
插件名称可以省略 `eslint-plugin-` 前缀  
引入第三方插件后即可在 extends 中指定使用
```
"plugins": [
    "plugin1",
    "eslint-plugin-plugin2"
]
```

### extends —— 基础配置
用于引入某配置作为基础配置，然后再在后续的rules中对其进行扩展
```
extends: 'eslint:recommended',
```

### rules —— 个性化配置
扩展（或覆盖）extends 中预定义的规则
```
rules: {
  eqeqeq: 1,  // if 判断语句间
  'no-console': 0, 
  'space-before-function-paren': [
    1,
    {
      anonymous: 'always',
      named: 'never',
      asyncArrow: 'always'
    }
  ],
  'generator-star-spacing': 0,
  'spaced-comment': 1,  // 注释之后先跟一空格 在写注释内容
  indent: [1, 2], 
  'no-unused-vars': 1,
  semi: [1, 'never'],
  'no-multiple-empty-lines': [1, { max: 2 }], // 不允许多个空行
  'comma-dangle': [1, 'never'], // 要求或禁止使用拖尾逗号
  'no-multi-spaces': 1, // 禁止出现多个空格
  'no-debugger': process.env.NODE_ENV === 'production' ? 0 : 1 // allow debugger during development
},
```

### 通用配置
插件依赖： `eslint babel-eslint`
```
module.exports = {
  root: true,
  parser: 'babel-eslint',
  parserOptions: {
    ecmaVersion: 6,
    sourceType: 'module'
  },
  env: {
    browser: true,
    node: true,
    es6: true,
    commonjs: true
  },
  globals: {},
  plugins: [],
  extends: 'eslint:recommended',
  rules: {
    eqeqeq: 1,
    'no-console': 0,
    'space-before-function-paren': [
      1,
      {
        anonymous: 'always',
        named: 'never',
        asyncArrow: 'always'
      }
    ],
    'generator-star-spacing': 0,
    'spaced-comment': 1,
    indent: [1, 2],
    'no-unused-vars': 1,
    semi: [1, 'never'],
    'no-multiple-empty-lines': [1, { max: 2 }],
    'comma-dangle': [1, 'never'],
    'no-multi-spaces': 1,
    'no-debugger': process.env.NODE_ENV === 'production' ? 0 : 1
  }
}
```

### React
依赖插件 `eslint-plugin-jsx-a11y`
```
parser: 'babel-eslint',
plugins: ['jsx-a11y'],
extends: ['react-app', 'plugin:jsx-a11y/recommended'],
```

### Vue
[eslint-plugin-vue](https://github.com/vuejs/eslint-plugin-vue)
依赖 `eslint-plugin-vue` 插件
```
parserOptions: {
  sourceType: 'module',
  parser: 'babel-eslint',
},
plugins: ['vue'],
extends: ['plugin:vue/essential', 'eslint:recommended'],
```

### 微信小程序
```
globals: {
  __DEV__: true,
  __WECHAT__: true,
  __ALIPAY__: true,
  App: true,
  Page: true,
  Component: true,
  Behavior: true,
  wx: true,
  getApp: true
},
```

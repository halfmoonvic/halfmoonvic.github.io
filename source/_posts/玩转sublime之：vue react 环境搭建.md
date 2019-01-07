---
title: 玩转sublime之：vue react 环境搭建
date: 2019-01-07 16:00:10
category: IDE
tags:
---

vue开发团队在sublime上的插件开发 仅有 `Vue Syntax Highlight` 语法高亮 一项，而未像 `vscode` 上的 `vetur` 插件那般 `Syntax-highlighting Snippet Emmet Linting / Error Checking Formatting Auto Completion Debugging` 等诸多功能，各项功能均需自己配置

### * 语法高亮
1. `Vue Syntax Highlight`  [🔗️ 278k](https://packagecontrol.io/packages/Vue%20Syntax%20Highlight)
    * 如果 `script` 标签标记为 `type="text/ecmascript-6"` 或 `type="text/babel"`，在新版的 `Vue Syntax Highlight` 则无法对 js 代码进行高亮
    * 旧版的 `type="text/ecmascript-6"` 解决方法是向 `vue.tmLanguage` 文件当中添加 `|ecmascript` 选项 [🔗️](https://github.com/vuejs/vue-syntax-highlight/blob/master/vue.tmLanguage#L832)
2. `Babel` [🔗️ 500k](https://packagecontrol.io/packages/Babel)

### * 校验 [参考链接 🔗️](https://halfmoonvic.github.io/2018/08/05/eslint-%E9%85%8D%E7%BD%AE%E4%BB%8B%E7%BB%8D/)
#### 1) sublime 插件 `SublimeLinter-eslint` [🔗️ 153k](https://packagecontrol.io/packages/SublimeLinter-eslint)
This linter plugin for SublimeLinter provides an interface to ESLint

1. npm 包 `eslint` [🔗️](https://github.com/eslint/eslint)
2. npm 包 `eslint-plugin-vue` [🔗️](https://github.com/vuejs/eslint-plugin-vue)
```
plugins: ['vue'],
extends: ['plugin:vue/essential', 'eslint:recommended'],
```

#### 2) sublime 插件 `Sublime​Linter-tslint` [🔗️ 17k](https://packagecontrol.io/packages/SublimeLinter-tslint)
This linter plugin for SublimeLinter provides an interface to tslint. It will be used with files that have the “typescript” syntax.

1. npm 包 `tslint` `tslint.json`
```
{
  "defaultSeverity": "error",
  "extends": [
    "tslint:recommended",
    "tslint-config-prettier"
  ],
  "rules": {
  }
}
```


### * 格式化
#### 一、 `HTML-CSS-JS Prettify` [🔗️ 879k](https://packagecontrol.io/packages/HTML-CSS-JS%20Prettify)
##### 1) 简介
1. HTML, CSS, JavaScript, JSON, React and Vue code formatter for Sublime Text 2 and 3 via node.js
2. 其对 js 的格式化是 通过 [js-beautify](https://github.com/einars/js-beautify) 来进行设置的

##### 2) 偏好设置
其 node 路径通过 `Packages/node_env.py` 来设置
```
User\HTMLPrettify.sublime-settings
----------
{
    // Simply using `node` without specifying a path sometimes doesn't work :(
    // https://github.com/victorporof/Sublime-HTMLPrettify#oh-noez-command-not-found
    // http://nodejs.org/#download
    "node_path":
    {
        "windows": "C:/Program Files/nodejs/node.exe",
        "linux": "/usr/bin/nodejs",
        "osx": "node"
    },
    // Automatically format when a file is saved.
    "format_on_save": false,
}
```

##### 3) `.jsbeautifyrc` 格式化规则 [🔗️](https://github.com/beautify-web/js-beautify#setting-inheritance-and-language-specific-overrides)
The plugin looks for a .jsbeautifyrc file in the following directories:

1. The same directory as the source file you're prettifying.
2. The source file's parent directories.
3. Your home folder.
4. Your personal Sublime settings folder.


#### 二、 `JsPrettier` [🔗️ 55k](https://packagecontrol.io/packages/JsPrettier)
##### 1) 偏好设置
1. `JsPrettier` 的偏好设置文件是一些类似设置 node prettier 路径的配置和 `Prettier Options` 选项
2. `node prettier` 路径有 `Packages/node_env.py` 来设置了，文件中可直接留空
```
JsPrettier.sublime-settings
----------
"prettier_cli_path": "",
// "prettier_cli_path": "/Users/john/.nvm/versions/node/v10.13.0/bin/prettier",
"node_path": "",
// "node_path": "/Users/john/.nvm/versions/node/v10.13.0/bin/node",
some other config .....
```
##### 2) `Prettier Options` 格式化规则 [🔗️](https://prettier.io/docs/en/options.html)

#### 三、 `JsFormat` [🔗️ 666k](https://packagecontrol.io/packages/JsFormat)
##### 1) 简介
JsFormat is a javascript formatting plugin for Sublime Text 2. **It uses jsbeautifier** to format whole js or json files, or the selected portion(s).

##### 2) 偏好设置
其偏好设置 亦是一些 诸如 `auto_formatter` 的选项 和 `Pretter Options`

### * 补全
#### 一、 VUE
vue 补全主要依赖于 `User\Snippets` 中的内容
```
// the third library
// the local function and variables
// the local component
```

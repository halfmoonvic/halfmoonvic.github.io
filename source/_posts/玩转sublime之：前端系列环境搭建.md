---
title: 玩转sublime之：前端系列环境搭建
date: 2019-01-07 16:00:10
category: IDE
tags:
---

### HTML 相关
#### 一、 `Emmet` [🔗️ 3.99m](https://packagecontrol.io/packages/Emmet)
##### 1) 简介
1. 前端开发神器
2. 可以快速补全HTML、css　　

##### 2) [快捷键](https://github.com/sergeche/emmet-sublime/blob/master/README.md#how-to-expand-abbreviations-with-tab-in-other-syntaxes)
为使其在 jsx 当中依然起作用，需在 `Default (OSX).sublime-keymap || Default (Windows).sublime-keymap` 中做如下设置
```
{
    "keys": ["tab"],
    "command": "expand_abbreviation_by_tab",

    // put comma-separated syntax selectors for which
    // you want to expandEmmet abbreviations into "operand" key
    // instead of SCOPE_SELECTOR.
    // Examples: source.js, text.html - source
    "context": [
        {
            "operand": "source.js",
            "operator": "equal",
            "match_all": true,
            "key": "selector"
        },

        // run only if there's no selected text
        {
            "match_all": true,
            "key": "selection_empty"
        },

        // don't work if there are active tabstops
        {
            "operator": "equal",
            "operand": false,
            "match_all": true,
            "key": "has_next_field"
        },

        // don't work if completion popup is visible and you
        // want to insert completion with Tab. If you want to
        // expand Emmet with Tab even if popup is visible --
        // remove this section
        {
            "operand": false,
            "operator": "equal",
            "match_all": true,
            "key": "auto_complete_visible"
        },
        {
            "match_all": true,
            "key": "is_abbreviation"
        }
    ]
},
```

#### 一、 `SublimeTmpl` [🔗️ 122k](https://packagecontrol.io/packages/SublimeTmpl)
##### 1) 简介
1. 快速生成文件模板
2. 可以快速补全HTML、css

##### 2) 快捷键
```
ctrl+alt+h html
ctrl+alt+j javascript
ctrl+alt+c css
ctrl+alt+p php
ctrl+alt+r ruby
ctrl+alt+shift+p python
```

----------

### CSS 相关
#### 一、 `cssrem` [🔗️](https://github.com/flashlizi/cssrem)
##### 1) 简介
1. 将 CSS 的 px 值转 rem 值
2. 需手动安装在 package 目录下即可

##### 2) 偏好设置
```
User\cssrem.sublime-settings
// CssRem 在 HTML 的 style 中 起作用
{
    "px_to_rem": 40,
    "max_rem_fraction_length": 6,
    "available_file_types": [".css", ".less", ".sass", ".html", ".vue"]
}
```

#### 二、 `Color Highlighter` [🔗️ 834k](https://packagecontrol.io/packages/Color%20Highlighter)
##### 1) 简介
1. 颜色提示插件,击代码中的颜色标记，就会在该标记上显示对应的颜色
2. 在颜色代码的后面显示颜色方块

##### 2) 偏好设置
<details>
<summary>(点击查看)User\ColorHighlighter.sublime-settings</summary>
```
{
    "file_extensions":
    [
        ".css",
        ".less",
        ".scss",
        ".sass",
        ".styl",
        ".vue",
        ".wxss",
        ".js"
    ],
    "search_colors_in":
    {
        // 正常状态
        "all_content":
        {
            "color_highlighters":
            {
                // 填充 文字 下划线，哪个方式显示颜色
                // filled text underlined_squiggly
                "color_scheme":
                {
                    "enabled": true,
                    "highlight_style": "text"
                },
                // 侧边栏显示颜色
                // Circle or Square
                "gutter_icons":
                {
                    "enabled": true,
                    "icon_style": "circle"
                },
                // 显示颜色块
                "phantoms":
                {
                    "enabled": false,
                    "length": 2,
                    "style": "right"
                }
            },
            // 总开关，是否启用这一项
            "enabled": true
        },
        // hover悬浮状态
        "hover":
        {
            "color_highlighters":
            {
                "color_scheme":
                {
                    "enabled": true,
                    "highlight_style": "filled"
                },
                "gutter_icons":
                {
                    "enabled": true,
                    "icon_style": "circle"
                },
                "phantoms":
                {
                    "enabled": true,
                    "length": 2,
                    "style": "right"
                }
            },
            "enabled": false
        },
        // 选中状态
        "selection":
        {
            "color_highlighters":
            {
                "color_scheme":
                {
                    "enabled": false,
                    "highlight_style": "underlined_squiggly"
                },
                "gutter_icons":
                {
                    "enabled": true,
                    "icon_style": "circle"
                },
                "phantoms":
                {
                    "enabled": true,
                    "length": 2,
                    "style": "right"
                }
            },
            "enabled": true
        }
    }
}
```
</details>


#### 二、 `Minify` [🔗️ 89k](https://packagecontrol.io/packages/Minify)
##### 1) 简介
1. Minify for Sublime Text allows you to quickly minify and/or beautify CSS, JavaScript, JSON, HTML and SVG files -- compatible with ST2 and ST3
2. Minify for Sublime Text can create a minified version of a currently open CSS, HTML, JavaScript, JSON or SVG file.
3. 这个很像之前早起的 gulp，生成 min 文件

##### 2) 使用
1. 安装 node　
2. npm install -g clean-css-cli uglifycss js-beautify html-minifier uglify-js minjson svgo

##### 3) 快捷键
1. `Command Palette` 中 输入 minify，生成 name.min.ext
2. `Command Palette` 中 输入 beautify，生成 name.pretty.ext

#### 三、 `less sass scsss stylus` 语法高亮
1. `LESS`      [🔗️ 599k](https://packagecontrol.io/packages/LESS)
2. `Sass`      [🔗️ 996k](https://packagecontrol.io/packages/Sass)
3. `SCSS`      [🔗️ 484k](https://packagecontrol.io/packages/SCSS)
4. `Stylus`    [🔗️ 141k](https://packagecontrol.io/packages/Stylus)

#### 四、 `SASS Build` [🔗️ 169k](https://packagecontrol.io/packages/SASS%20Build)
##### 1) 简介
1. 编译 scss sass 文件至 css
2. 当文件当中含有未在当前文件定义的 变量 宏等的时候无法编译通过
3. Ctrl ＋ B 编译Sass文件，提示编译错误，前提是已经安装好了Ruby环境与sass.gem

##### 2) 使用
1. 安装 ruby
2. `gem install sass`
3. sublime 安装插件
4. `super + b` 即可运行


#### 五、 `Less2Css`  [🔗️ 101k](https://packagecontrol.io/packages/Less2Css)
##### 1) 简介
编译 less 文件 至 css 文件,在保存的时候即自动编译
##### 2) 使用
###### 1. Windows
1. 下载 [less.js-windows](https://github.com/duncansmart/less.js-windows)至任意目录`aa`。
2. 将 目录 `aa` 添加至环境变量当中
3. 命令行 输入 `lessc`，输出 `lessc: no input files` 等信息则为成功
4. sublime 安装相应的插件
5. 注意，在保存 less 文件时，如果报 找不到 node 之类的错误，可直接将 `node.exe` 放入 `less-js-windows\bin` 目录下

##### 2. mac 安装 参考资料：[`lessc` is not avavailable](https://github.com/timdouglas/sublime-less2css/issues/55)
1. 安装 node　`brew install node`
2. 安装 less　`npm install less -gd`
3. 如果报 `less2css error: lessc is not avavailable` 错误，则直接修改 `User\less2css.sublime-settings` 文件当中 的 `lesscCommand` 选项至 `lessc` 的安装路径
4. 还会报 `node env` 的问题，插件是更改系统的环境来修复的，遂不在操作，此问题可以通过 `node_env.py`脚本来设置环境变量已解决
5. `less2css.sublime-settings` 文件中 默认 `"minify": true,`，固需 安装 `less-plugin-clean-css`，即 `npm install -g less-plugin-clean-css`
6. 另外，在`build`当中 新建了 `lessc.sublime-build` 文件 以替代此插件


----------

### JS 相关

#### 一、 语法高亮
1. `Babel`                  [🔗️ 500k](https://packagecontrol.io/packages/Babel)
2. `TypeScript`             [🔗️ 380k](https://packagecontrol.io/packages/TypeScript)


#### 二、 `DocBlockr`  [🔗️ 941k](https://packagecontrol.io/packages/DocBlockr)
##### 1) 简介
自动生成PHPDoc风格的注释。Javascript, PHP, ActionScript, Java, Objective C, C, C++
##### 2) 偏好设置
```
User/Base File.sublime-settings
----------
// DocBlockr设置 ctrl+enter,放到 package.user目录下
{
  // If true, then pressing enter while in a double-slash comment (like this one)
  // will automatically add two slashes to the next line as well
  "jsdocs_extend_double_slash": false,


  // If set to true, Ctrl+Enter while in a single line comment will 'decorate' the comment.
  "jsdocs_decorate": false,
}
```

----------

## 格式化

#### 一、 `HTML-CSS-JS Prettify`  [🔗️ 879k](https://packagecontrol.io/packages/HTML-CSS-JS%20Prettify)
##### 1) 简介
HTML+CSS+JAVASCRIPT+JSON快速格式化
##### 2) 格式化的偏好设置
`.jsbeautifyrc`

#### 二、 `JsFormat`  [🔗️ 666k](https://packagecontrol.io/packages/JsFormat)
##### 1) 简介
HTML+CSS+JAVASCRIPT+JSON快速格式化
##### 2) 偏好设置
`JsFormat.sublime-settings`

#### 三、 `JsPrettier`  [🔗️ 55k](https://packagecontrol.io/packages/JsPrettier)
##### 1) 简介
1. 2017 火起来了，格式化有自己的规则，可供定义的很少。 支持jsx语法格式话，对齐中的 html 做到很好的格式化。 需要全局安装 npm install -g prettier
2. 其本身仅是提供了一个面向 本地 `prettier` 包的一个接口。其本身不做格式化，而是驱使 `prettier` 包进行文件的格式化

##### 2) 偏好设置 `JsPrettier.sublime-settings` `prettier_cli_path` 的设置
* 假设一个项目的开发使用 prettier 做格式化，那么，我们希望的是一个人自进行 `npm install` 之后便可以在项目当中进行格式化的操作了，而不是说还要依赖于项目之外的插件
* `prettier_cli_path` 最好设置为空，这样 `jsPrettier` 插件 会依次在以下几个目录下面寻找 `prettier` 包
    + "prettier_cli_path": "/usr/local/bin/prettier"
    + "prettier_cli_path": "/some/absolute/path/to/node_modules/.bin/prettier"
    + "prettier_cli_path": "./node_modules/.bin/prettier"
    + "prettier_cli_path": "~/bin/prettier"
    + "prettier_cli_path": "$HOME/bin/prettier"
    + "prettier_cli_path": "${project_path}/bin/prettier"
    + "prettier_cli_path": "$ENV/bin/prettier"　
    + 总之是先在当前目项目录下寻找 prettier 目录下设置，没有找到的情况下在在全局包当中寻找。如果均为找到，则会直接报错找不到 `prettier` 包了
* sublime 当中想要在项目当中就能够找到 `prettier` 包 是需要将 将当前的 项目目录拖放到 Sublime 的 Side_Bar 上面的，目测，这个是 `${project_path}/bin/prettier` 这条命令的作用
* 当 Sublime Side_Bar 上仅有一个`A项目`时，其它项目中的文件所使用的 prettier 亦是 `A项目` 的，不过，如果当前文件所在项目目录也在 Side_Bar 上时，各自用各自的 prettier

##### 3) 补充说明
prettier 不同版本最后格式化的结果有可能是不一样的 {"printWidth": 120,}
比如 如下代码 
```
<template functional>
  <span class="age">
    <i class="iconfont icon-cake"/>
    {{props.value}}岁
  </span>
</template>
```
prettier@1.16.4 校验后为 
```
<template functional>
  <span class="age">
    <i class="iconfont icon-cake" />
    {{ props.value }}岁
  </span>
</template>
```
prettier@1.15.3 校验后为 
```
<template functional>
  <span class="age"> <i class="iconfont icon-cake" /> {{ props.value }}岁 </span>
</template>
```

#### 四、 `Pretty JSON` [🔗️ 548k](https://packagecontrol.io/packages/Pretty%20JSON)
##### 1) 简介
1. Prettify/Minify/Query/Goto/Validate/Lint JSON
2. 文件未保存的时候也可以进行格式化

----------

### 补全
#### 一、 `SublimeCodeIntel`  [🔗️ 1.42m](https://packagecontrol.io/packages/SublimeCodeIntel)
##### 1) 简介
1. 代码补全工具，补全时可提示代码类型
2. 可以结合 sublimecodeIntel 出来提示的时候，它在后面添加名字
3. css 中 分号 也会弹出补全，所以将其忽略
4. [Javascript completing after semi-colon](https://github.com/SublimeCodeIntel/SublimeCodeIntel/issues/461)

#### 二、 `CodeComplice`  [🔗️ 13k](https://packagecontrol.io/packages/CodeComplice)
解决 sublimecodeIntel 与 Anaconda 冲突的问题，直接忽略 sublimecodeIntel 插件就可以了

#### 三、 `Nodejs`  [🔗️ 286k](https://packagecontrol.io/packages/Nodejs)
##### 1) 简介
1.  node 的一些常用 snippets. alt+r 运行当前js文件。 alt+d 以debug模式运行当前js文件

#### 四、 `React ES6 Snippets`  [🔗️ 63k](https://packagecontrol.io/packages/React%20ES6%20Snippets)
##### 1) 简介
1. react 语法提示
2. Snippets 当中已经将其进行了个性化设置，固可已省略

----------

### 校验 [🔗️](https://halfmoonvic.github.io/2018/08/05/eslint-%E9%85%8D%E7%BD%AE%E4%BB%8B%E7%BB%8D/)
#### 一、 `SublimeLinter`  [🔗️ 1.74m](https://packagecontrol.io/packages/SublimeLinter)
##### 1) 简介
1. The code linting framework
2. 其本身并不提供代码校验，他就是一个架子。
3. 通过安装其 旗下的 插件来 提供 与 相应的包的接口 来进行校验

##### 2) 偏好设置
<details>
<summary>点击查看 User\SublimeLinter.sublime-settings</summary>
```
{
    // Lint Mode determines when the linter is run.
    // - background: asynchronously on every change
    // - load_save: when a file is opened and every time it's saved
    // - manual: only when calling the Lint This View command
    // - save: only when a file is saved
    "lint_mode": "load_save",
    "paths": {
        "linux": [],
        // $PATH 应该是直接将 node_env.py 的 $PATH 拿了过来
        // "osx": ["/usr/local/bin:$PATH", "/Users/john/go/bin"],
        "osx": ["$PATH", "/Users/john/go/bin"],
        "windows": ["D:\\_Git\\Go\\bin"]
    },
    "linters": {
        "golint": {
            // "env": {"PATH": "D:\\_Git\\Go\\bin"},
            "selector": "source.go - meta.attribute-with-value"
        },
        "tslint": {
            "disable": true,
            "selector": "text.html.vue, source.ts - meta.attribute-with-value",
        },
        "eslint": {
            "disable": false,
            // "env": {"PATH": ""},
            "selector": "text.html,text.vue,source.js,source.ts"
        },
        "jshint": {
            "selector": "source.js - meta.attribute-with-value"
        },
        "csslint": {
            "args": [],
            "errors": "",
            "excludes": [],
            "ignore": "",
            "ignored": [],
            "warnings": "",
            "selector": "source.css - meta.attribute-with-value"
        },
        "scsslint": {
            "disable": true,
            "selector": "source.scss,source.sass - meta.attribute-with-value"
        },
        "sass": {
            "disable": true,
            "selector": "source.scss,source.sass - meta.attribute-with-value"
        },
        "stylelint": {
            "disable": false,
            "selector": "text.html.vue,source.scss,source.sass - meta.attribute-with-value"
        },
    },
    "styles": [
        {
            // 'fill', 'outline', 'solid_underline', 'squiggly_underline', 'stippled_underline', 'none'
            "priority": 1,
            // 设置颜色，根据不同的主题，其颜色不一样 ＝ ＝
            "scope": "region.yellowish markup.warning.sublime_linter",
            // The icon displayed in the gutter area
            // - "circle", "dot" or "bookmark" - "none" to remove the icon
            // - A path to an icon file like
            //   "Packages/SublimeLinter/gutter-themes/Blueberry Cross/error.png"
            // - One provided by a gutter theme (e.g. "warning" or "error").
            //   In theme Default: warning, error, cog, x,
            //   and diamond, heart, pointer, square, star, triangle, which all
            //   also have an -outline variant.
            "icon": "dot",
            "types": [
                "warning"
            ]
        },
        {
            "mark_style": "outline",
            "priority": 1,
            "scope": "region.redish markup.deleted.sublime_linter markup.error.sublime_linter invalid",
            "icon": "dot",
            "types": [
                "error"
            ]
        }
    ],
}
```
</details>

偏好设置主要是针对是否开启当前校验及其环境变量的设置

##### 3) 偏好设置详细说明
1. 此偏好设置同时开启了 tslint 与 eslint，且都可以 校验 vue 文件。但是，根据不同语言（js、ts)编写的 vue 文件需要扩展不同的第三方包。
    * js 编写的则只需 安装 vue 官方的 plugin 即可，如 `eslint-plugin-vue`，此时 eslint 便可以做出很好的校验了。如果说想要用 prettier 来校验，相应的 还可以 安装 `eslint-config-prettier` 做扩展
    * ts 编写的 vue 文件，如果确定还是 使用 tslint 来做校验而非 eslint。则不需要安装什么第三方插件了，vue 官方也未出台 类似 `eslint-plugin-vue` 这样的插件（这样 tslint 会校验 vue 文件的所有部分，包括 html css 部分）。当然，还是可以 安装 `tslint-config-prettier` 来做 `extends` 扩展
    * tslint 是一个逐渐被放弃的，一些 ts 的校验也逐渐在使用 eslint 来做校验。详见下一条
2. [tslint](https://eslint.org/blog/2019/01/future-typescript-eslint) 逐渐被放弃使用，转而使用 eslint 来校验 ts 文件，相应的 parser 则为 [@typescript-eslint/parser ](https://github.com/typescript-eslint/typescript-eslint)，较为详细的 安装步骤 可 参看 [@typescript-eslint/eslint-plugin](https://www.npmjs.com/package/@typescript-eslint/eslint-plugin)
3. 一个 linter 想要生效其有时候借助了许多 插件包的，比如在一个 ts 书写的 vue 文件当中如果没有相应的 `@typescript-eslint/parser` 包等，eslint 是无法校验其大部分代码的。
4. 由于 此偏好设置 同时开启了 tslint 与 eslint，所以导致如果一些项目主导是用 eslint 来做校验的，没有安装 tslint 的一些包，这就导致了其有可能在控制台报错缺少 tslint 插件的安装。反之 eslint 亦然

#### 二、 `SublimeLinter-csslint`  [🔗️ 188k](https://packagecontrol.io/packages/SublimeLinter-csslint)
css 错误提示插件 `npm install -g csslint`

#### 三、 `Sublime​Linter-contrib-scss-lint || SublimeLinter-scss-lint`  [🔗️ 45k](https://packagecontrol.io/packages/SublimeLinter-contrib-scss-lint)
This linter plugin for SublimeLinter provides an interface to the scss-lint. It will be used with files that have the CSS, SASS and SCSS syntax.  

1. Install Ruby.
2. Install scss-lint by typing the following in a terminal: `gem install scss_lint`
3. scss-lint 是 ruby 包，项目当中就不可以使用了，仅能够全局使用

#### 四、 `Sublime​Linter-contrib-sass-lint` [🔗️ 45k](https://packagecontrol.io/packages/SublimeLinter-contrib-sass-lint)
This linter plugin for SublimeLinter provides an interface to sass-lint (Node.js). It will be used with files that have Sass and SCSS syntax.
1. `npm install -g sass-lint`
2. sass-lint 是 node 包

#### 五、 `Sublime​Linter-stylelint` [🔗️ 14k](https://packagecontrol.io/packages/SublimeLinter-stylelint)
This linter plugin for SublimeLinter provides an interface to stylelint.  
`stylelint` 的设计与 `eslint` 大致类似。不同于 `sass-lint` `scss_lint`，`stylelint` 不仅可在 编辑上得到支持，还可在 终端、webpack 等编译工具上得到支持。这使得其亦相 `eslint` 般得到广泛支持

1. `npm install postcss stylelint`

#### 五、 `SublimeLinter-jshint`  [🔗️ 334k](https://packagecontrol.io/packages/SublimeLinter-jshint)
1. 依赖包 `npm install -g jshint`
2. js 错误提示插件，es6 语法也会警告 `arrow function syntax (=>) is only available in  ES6 (use 'esversion:6')`
3. 所以将其忽略了

#### 六、 `SublimeLinter-eslint`  [🔗️ 152k](https://packagecontrol.io/packages/SublimeLinter-eslint)
1. 依赖包 `npm install -g eslint`
2. 其要求有一个项目配置文件，直接放到 根目录下面即可。如果没有项目配置文件，sublime 控制台报错如下信息 ` ESLint couldn't find a configuration file. To set up a configuration file for this project, please run: eslint --init`

<details>
<summary>点击查看 根目录下的配置文件</summary>
```
module.exports = {
  root: true,
  parserOptions: {
    parser: 'babel-eslint',
    sourceType: 'module',
    ecmaVersion: 2017
  },
  env: {
    browser: true,
    node: true,
    es6: true,
    commonjs: true
  },
  plugins: ['vue'],
  extends: ['plugin:vue/essential', 'eslint:recommended'],
  rules: {
    'comma-dangle': [1, 'never'],
    eqeqeq: 1,
    indent: [0, 2],
    'generator-star-spacing': 0,
    'no-console': 0,
    'no-debugger': process.env.NODE_ENV === 'production' ? 0 : 1,
    'no-multiple-empty-lines': [1, { max: 2 }],
    'no-multi-spaces': 1,
    'no-unused-vars': 1,
    semi: [0, 'never'],
    'space-before-function-paren': [
      1,
      {
        anonymous: 'never',
        named: 'never',
        asyncArrow: 'always'
      }
    ],
    'spaced-comment': 1,
  }
}
```
</details>

#### 七、 `SublimeLinter-tslint`  [🔗️ 17k](https://packagecontrol.io/packages/SublimeLinter-tslint)
1. 依赖包 `npm install -g tslint`
2. 其要求有一个项目配置文件 `tslint.json`，同 `SublimeLinter-eslint`
3. `tslint` 包 无法区分一个文件当中的 html css 部分，固忽略了

<details>
<summary>点击查看 根目录下的配置文件</summary>
{
  "defaultSeverity": "error",
  "extends": [
    "tslint:recommended"
  ],
  "linterOptions": {
    "exclude": [
      "node_modules/**"
    ]
  },
  "rules": {
    "prettier": true
  }
}
</details>

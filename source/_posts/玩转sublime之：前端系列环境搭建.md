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
```
User\ColorHighlighter.sublime-settings
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

#### 二、 `Minify` [🔗️ 89k](https://packagecontrol.io/packages/Minify)
##### 1) 简介
1. Minify for Sublime Text allows you to quickly minify and/or beautify CSS, JavaScript, JSON, HTML and SVG files -- compatible with ST2 and ST3

#### 三、 `less sass scsss stylus` 语法高亮
1. `LESS`      [🔗️ 599k](https://packagecontrol.io/packages/LESS)
2. `Sass`      [🔗️ 996k](https://packagecontrol.io/packages/Sass)
3. `SCSS`      [🔗️ 484k](https://packagecontrol.io/packages/SCSS)
4. `Stylus`    [🔗️ 141k](https://packagecontrol.io/packages/Stylus)

#### 四、 `SASS Build` [🔗️ 169k](https://packagecontrol.io/packages/SASS%20Build)
##### 1) 简介
编译 scss sass 文件至 css

Ctrl ＋ B 编译Sass文件，提示编译错误，前提是已经安装好了Ruby环境与sass.gem
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
6. 另外，在`build`当中 新建了 `lessc.sublime-build` 文件 以替代


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
Base File.sublime-settings
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
##### 2) 偏好设置
`.jsbeautifyrc`

#### 二、 `JsFormat`  [🔗️ 666k](https://packagecontrol.io/packages/JsFormat)
##### 1) 简介
HTML+CSS+JAVASCRIPT+JSON快速格式化
##### 2) 偏好设置
`JsFormat.sublime-settings`

#### 三、 `JsPrettier`  [🔗️ 55k](https://packagecontrol.io/packages/JsPrettier)
##### 1) 简介
1. 2017 火起来了，格式化有自己的规则，可供定义的很少。 支持jsx语法格式话，对齐中的 html 做到很好的格式化。 需要全局安装 npm install -g prettier

#### 四、 `Pretty JSON` [🔗️ 548看](https://packagecontrol.io/packages/Pretty%20JSON)
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

#### 二、 `SublimeLinter-csslint`  [🔗️ 188k](https://packagecontrol.io/packages/SublimeLinter-csslint)
css 错误提示插件 `npm install -g csslint`

#### 三、 `Sublime​Linter-contrib-scss-lint || SublimeLinter-scss-lint`  [🔗️ 45k](https://packagecontrol.io/packages/SublimeLinter-contrib-scss-lint)
scss 错误提示插件 `npm install -g csslint`

1. Install Ruby.
2. Install scss-lint by typing the following in a terminal: `gem install scss_lint`

#### 三、 `SublimeLinter-jshint`  [🔗️ 334k](https://packagecontrol.io/packages/SublimeLinter-jshint)
1. 依赖包 `npm install -g jshint`
2. js 错误提示插件，es6 语法也会警告 `arrow function syntax (=>) is only available in  ES6 (use 'esversion:6')`
3. 所以将其忽略了

#### 四、 `SublimeLinter-eslint`  [🔗️ 152k](https://packagecontrol.io/packages/SublimeLinter-eslint)
1. 依赖包 `npm install -g eslint`
2. 其要求有一个项目配置文件，直接放到 根目录下面即可。如果没有项目配置文件，sublime 控制台报错如下信息 ` ESLint couldn't find a configuration file. To set up a configuration file for this project, please run: eslint --init`

#### 五、 `SublimeLinter-tslint`  [🔗️ 17k](https://packagecontrol.io/packages/SublimeLinter-tslint)
1. 依赖包 `npm install -g tslint`
2. 其要求有一个项目配置文件 `tslint.json`，同 `SublimeLinter-eslint`
3. `tslint` 包 无法区分一个文件当中的 html css 部分，固忽略了

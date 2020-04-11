---
title: 玩转sublime之：LSP 实现 ide 功能
date: 2020-04-11 17:06:10
category: IDE
tags:
---

#### 一、 What is LSP
The Language Server protocol is used between a tool (the client) and a language smartness provider (the server) to integrate features like auto complete, go to definition, find all references and alike into the tool

### 二、 运行原理
1. ide(sublime 等) 与 语言服务，比如 `golang` 进行通讯，以便完成诸如 `auto complete` `go to definition` `find all references` 等功能需要一个协议，这个协议便是 `lsp —— Language Server protocol`

2. 一般的一些 ide 实现对一些语言的功能都是编写了一些插件  
    * 比如 `sublime` 对 `golang 插件` 的支持，主要是由 `GoSublime` 这个插件完成的，其可完成对 `go` 的自动补全等功能。
    * 而 `vscode` 则是需要安装 `go 插件`，且需要 安装 配套的一些包比如 `go get golang.org/x/tools/gopls`
    * 这样就造成的一些问题，比如，不同的编辑器都需要编写特定的 插件来实现对语言的支持，如果有 `n` 个编程器，`m` 个语言，则需要 `m * n` 个程序

3. `lsp` 提供了 `ide` 与 `language server` 之间的一个媒介
    * 比如 `golang` 实现了这套 `lsp` 协议，—— `gopls`
    * `ide` 支持 `lsp`，比如 `sublime` 可以通过安装 [`LSP`](https://github.com/sublimelsp/LSP)
    * 这样两者就链接了起来，`sublime` 就可以很好运行 `golang` 了

### 三、 `Vetur`
1. 方式一： 通过 安装 `LSP-vue` 就可以实现 `Vetur` 的功能。
2. 方式二： 也不需要安装 `LSP-vue` 插件，自信 `npm install -g vue-language-serve` 插件后，编写 `LSP.sublime-settings`，就可以实现。  
[Support TypeScript with Vue SFC in Sublime Text](https://www.louisbourque.ca/blog/2019/06/TypeScript-Vue-SFC-Sublime-Text/)

### 四、 `go`
1. 使用 `Sourcegraph's go-langserver`，`Gopls` 不能自动引入
    `go get golang.org/x/tools/gopls@latest`
    ```
    {
        "clients": {
            "golsp": {
                "enabled": true,
                "initializationOptions": {
                    // use go code
                    "gocodeCompletionEnabled": false
                },
                "settings": {
                    "only_show_lsp_completions": true
                }
            },
            "gopls": {
                "enabled": false
            },
        },
        "show_view_status": true
    }
    ```
2. `GoSublime` 自动导入一直有些问题，可以使用 `go-lsp` → `formatter` 来完成，但是并没有 `formatter on save` 的选项貌似

### 五、 参考资料

1. [Langserver.org](https://langserver.org/)
2. [什么是Language Server protocol（LSP）?](https://blog.csdn.net/u012930117/article/details/79291677)
3. [SublimeLsp doc](https://lsp.readthedocs.io/en/latest/)

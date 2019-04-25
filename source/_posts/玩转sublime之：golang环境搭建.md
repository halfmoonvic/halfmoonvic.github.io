---
title: 玩转sublime之：golang环境搭建
date: 2019-01-07 16:00:10
category: IDE
tags:
---

#### 一、 平台安装 golang
##### 1) mac 环境下
1. 安装 gvm [🔗️](https://github.com/moovweb/gvm)   
    `bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)
`
2. `.bash_profile`脚本中写入 `[[ -s "$HOME/.gvm/scripts/gvm" ]] && source "$HOME/.gvm/scripts/gvm"`，以便在命令行中可以使用 gvm
3. `gvm install go1.7.3 -B`
4. `gvm use go1.7.3`
5. `export GOROOT_BOOSTRAP=/Users/john/.gvm/gos/go1.7.3`
6. `gvm install goxxx`

##### 2) gvm 使用注意事项
1. 如果下载好 gvm 后，直接 `go install go1.11.4`，则会报编译报错。Go 1.5 发布，完全移除 C，使用 Go 编译 Go，所以，在下载安装最新的 go 的时候，需要先安装一个低版本的 的来编译
2. github说让安装 `go1.4` 版本即可，但是会报错 [fatal error: MSpanList_Insert](https://www.itread01.com/p/694355.html)，所以此处 安装了 1.7.3

##### 3) 环境变量配置
1. 用户本地目录                `export GOPATH=$HOME/Go`
2. go语言包的 配置到环境变量中    `export PATH=$PATH:$HOME/Go/bin`


#### `GoSublime`
##### 1) 简介
1. 代码自动补全 Gocode
2. 整合了GoSublime 90个命令提示符
3. 预付类型检查
4. 快速跳到语法错误
5. 快速格式化代码
6. 在 play.golang.org 上面快速分享代码片段
7. 在现在的文件中列举所有声明
8. 自动添加包的导入
9. 包中函数和变量的定位
10. 在同一个试图中查看函数和变量的定位

##### 2) 安装
1. 插件安装需要通过 git 直接进行安装 `git clone https://margo.sh/GoSublime` [🔗️](https://margo.sh/b/migrate/)
2. 依赖包 `goimports`: `go get golang.org/x/tools/cmd/goimports`

##### 3) 偏好设置
```
User\GoSublime.sublime-settings
----------
{
    // "env": {
    //     "GOPATH": "C:\\Program Files\\Go",
    //     "GOROOT": "D:\\_Git\\Go"
    // },
    "env": {
        "GOPATH": "/Users/john/go",
        "GOROOT": "/Users/john/.gvm/gos/go1.11.2"
    },
    "fmt_enabled": true,
    "fmt_cmd": ["goimports"]
}
```


#### `Godef`
##### 1) 简介
找到 golang 自带的函数及其个人自定义的
##### 2) 安装
1. 依赖包 `go get -v github.com/rogpeppe/godef`
2. 依赖包 `go get -v golang.org/x/tools/cmd/guru`
3. Godef 先使用 查找速度更快的 godef 包查找，如果没找到，在使用 guru　
##### 3) 偏好设置
```
User\Godef.sublime-settings
{
    // Copy this file to Settings - User
    // Set your real GOPATH in user's Godef.sublime-settings
    // For example:

    "goroot": "/Users/john/.gvm/gos/go1.11.2",
    "gopath": "/Users/john/Go",

    // Or if you use windows
    // "goroot": "C:\\Go",
    // "gopath": "C:\\gopath",
}
```


#### 校验
##### 1) `SublimeLinter-golint` [🔗️](https://packagecontrol.io/packages/SublimeLinter-golint)
依赖包 `go get -u golang.org/x/lint/golint` [`golint`](https://github.com/golang/lint)

---
title: 玩转sublime之：path环境变量设置
date: 2020-03-07 16:21:10
category: IDE
tags:
---

1. path 指定的就是各种 可执行文件所在的目录
2. os 中， $PATH 是一个全局的特殊变量，其值便是系统中各种环境变量

### 一、 关于 环境变量的一些 解释
当我们在终端运行某些程序时，比如 `eslint`，系统并不知道 `eslint` 是什么，这时系统会主动去环境变量所指定的目录下去寻找是否有 `eslint` 这个程序，有则直接运行相应目录的程序。  
即 环境变量 定义的就是一组目录，这个目录下存放着各种各样的程序，我们在运行一些指定的程序的时候，并不需要输入这个程序完整的路径来执行他，而仅是输入程序名称，系统便会在 这些目录下查询是否有这些程序了

### 二、 编辑环境变量 —— os 
通过编辑 `.bash_profile` 文件直接修改环境变量设置，如
```
export PATH=$PATH:$HOME/_Git/Go/bin
```
此语句便是将 `$HOME/_Git/Go/bin` 加入到环境变量中，如此，在此目录下的一些程序如 `golint` 等程序，在终端上直接输入 `golint` 便可访问该程序了

### 三、 编辑环境变量 —— Windows
Windows 编辑 环境变量则是通过 UI 界面来设置。  
`高级系统设置` → `系统属性-高级` → `环境变量` → 编辑 path 信息  
在此界面中有 `用户变量` 与 `系统变量` 两项，`用户变量` 中 path 设置 则是 仅当前 Windows用户可以使用了，而 `系统变量` 则是所有 Windows用户都可以使用。

### 四、 sublime 中环境变量设置 —— mac
sublime 默认获取的 的 path 为 一些系统级别的path，而用户所编写的 path，sublime 并不能获取到，这就导致了 如果我们使用了 nvm 来管理 node 则 一些 npm 包是无法探寻到的
```
$PATH
    /usr/local/bin
    /usr/bin
    /bin
    /usr/sbin
    /sbin
```
这个时候，我们 建立一个 python 文件，在 sublime 启动时运行，文件中将 $PATH 重新赋值。  
其所在位置便是 `Sublime Text 3/Packages/env_mac.py`  
```
# SOLVE NVM ENVIRONMENT OF NODEPATH
# reference https://gist.github.com/joesepi/11269417
# code by echenley commented on 17 Jun 2015
# which node /Users/john/.nvm/versions/node/v10.15.0/bin/node
# which prettier /Users/john/.nvm/versions/node/v10.15.0/bin/prettier
# "osx": "/usr/local/bin/node"  // line windows: you cd to some directory and run it
# "osx": "node"  // like windows: you put the node_path into Environmental variable

# reference https://gist.github.com/joesepi/11269417 and the node_env.py
# code by echenley commented on 17 Jun 2015
# add golang env to the path
# 2020.03.12

import os
import getpass
import re

user = getpass.getuser()

default_path = "/Users/%(user)s/bin:/usr/local/sbin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin" % {'user':user}

# node env
nvm_default_file_path = '/Users/%(user)s/.nvm/alias/default' % {'user': user}
with open(nvm_default_file_path, 'r') as content_file:
    nvm_content = content_file.read()
node_version = nvm_content.strip()
nodeenv_path = ":/Users/%(user)s/.nvm/versions/node/v%(node_version)s/bin:/Users/%(user)s/.nvm/versions/node/v%(node_version)s/lib" % {'node_version':node_version, 'user':user}


# golang env
gvm_go_file_environments = '/Users/%(user)s/.gvm/environments/default' % {'user': user}
with open(gvm_go_file_environments, 'r') as content_file:
    gvm_content = content_file.read()
go_version = re.findall('gvm_go_name=\"(go[\.\d]{1,6})\"', gvm_content.strip())[0]

goenv_path = ":/Users/%(user)s/.gvm/pkgsets/%(go_version)s/global/bin:/Users/%(user)s/.gvm/gos/%(go_version)s/bin:/Users/%(user)s/.gvm/pkgsets/%(go_version)s/global/overlay/bin:/Users/%(user)s/.Go/bin" % {'go_version':go_version, 'user':user}

path = default_path + nodeenv_path + goenv_path
os.environ["GOPATH"] = "/Users/%(user)s/.Go" % {'go_version':go_version, 'user':user}
os.environ["GOROOT"] = "/Users/%(user)s/.gvm/gos/%(go_version)s" % {'go_version':go_version, 'user':user}


# edit $PATH
os.environ["PATH"] = path
print(path)
```

sublime 在此输出 $PATH 时 如下
```
/Users/john/.nvm/versions/node/v10.15.3/bin
/Users/john/.nvm/versions/node/v10.15.3/lib
/Users/john/bin
/usr/local/sbin

/usr/local/bin
/usr/bin
/bin
/usr/sbin
/sbin
```

### 五、 sublime 中环境变量设置 —— window
1. window 下 sublime 可以拿到 我们自行设置的 用户环境变量，比如，我们设置 `GOPATH=C:\_Git\Go`
2. 但也可以设置 `env_windows.py` 自行设置 这种用户变量，以防止有些程序并不会自动在用户环境变量里去设置这种变量
3. `env_windows.py` 设置的变量是可以覆盖 在UI界面设置的用户环境变量
```
# SOLVE NVM ENVIRONMENT OF NODEPATH
# reference https://gist.github.com/joesepi/11269417
# code by echenley commented on 17 Jun 2015
# which node /Users/john/.nvm/versions/node/v10.15.0/bin/node
# which prettier /Users/john/.nvm/versions/node/v10.15.0/bin/prettier
# "osx": "/usr/local/bin/node"  // line windows: you cd to some directory and run it
# "osx": "node"  // like windows: you put the node_path into Environmental variable

# reference https://gist.github.com/joesepi/11269417 and the node_env.py
# code by echenley commented on 17 Jun 2015
# add golang env to the path
# 2020.03.12

import os
import getpass

user = getpass.getuser()

# os.environ["GOPATH"] = "C:/Users/John/Go" % {'user':user}
os.environ["GOPATH"] = "D:/_Git/Go"
os.environ["GOROOT"] = "%ProgramFiles%/Go"

# edit $PATH
# os.environ["PATH"] = path
print(os.environ["PATH"])
```

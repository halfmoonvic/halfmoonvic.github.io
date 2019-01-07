---
title: 玩转sublime之：sublime工具增强篇
date: 2019-01-07 16:00:10
category: IDE
tags:
---

#### 一、 `AdvancedNewFile` [🔗️ 521k](https://packagecontrol.io/packages/AdvancedNewFile)
##### 1) 简介
快速创建文件
##### 2) 快捷键
> windows -- ctrl+alt+n  
> mac     -- super+alt+n
##### 3) 偏好设置
可以创建别名及其默认别名以在不同的目录下快速的创建文件
```
User/AdvancedNewFile.sublime-settings
{
    // This setting contains a dictionary of aliases. The keys represent the
    // alias names, the values represent the paths.
    // NOTE: These should be absolute paths. Also, these paths should
    // match your systems convention. For example, Windows machines should
    // have paths similar to "C:\\Users\\username\\Desktop". *nix systems should
    // have paths similar to "/home/username/desktop".
    // 这个只能在单一的平台上使用，直接使用下面那个
    "alias": {
        // "d": "C:\\Users\\John\\Desktop",
        // "i": "D:\\_Nutstore\\_Inbox"
        // "j": "~",
        // "i": "~/_Nutstore/_Inbox"
    },
    // This is a secondary field for aliases. These aliases will be platform specific.
    // The key for the entry will still represent the name to be used for the alias.
    // Rather than being just a string path, the value will be a nested dictionary.
    // The dictionary may have one of three keys, "windows", "linux", or "osx".
    // The path used for this alias will be based on the operating system being used.
    "os_specific_alias": {
        "subl_packages": {
            "windows": "~\\AppData\\Roaming\\Sublime Text 3\\Packages",
            "linux": "~/.config/sublime-text-3/Packages",
            "osx": "~/Library/Application Support/Sublime Text 3/Packages"
        },
        "d": {
            "windows": "C:\\Users\\John\\Desktop",
            "osx": "~/Desktop",
        },
        "h": {
            "windows": "C:\\Users\\John",
            "osx": "~",
        },
        "i": {
            "windows": "D:\\_Nutstore\\_Inbox",
            "osx": "~/_Nutstore/_Inbox",
        },
        "w": {
            "windows": "D:\\Work",
            "osx": "~/Work",
        }
    },

    // A default initial value to fill the create new file input path with.
    "default_initial": "h:"
}
```

#### 二、 `AutoFileName` [🔗️ 895k](https://packagecontrol.io/packages/AutoFileName)
文件路径自动提示补全
##### 1) 快捷键
无
##### 2) 偏好设置
无


#### 三、 `ConvertToUTF8` ———— 显示中文
##### 1) 简介
windows/mac 平台下中文乱码问题解决方案
##### 2) 快捷键设置
`ConvertToUTF8` 默认快捷键 `super+shift+c` 与 `colorPicker` 相冲突
将以下三个文件拖放到 `Packages\ConvertToUTF8` 中以覆盖默认的快捷键设置
```
\Packages\ConvertToUTF8\Default (Linux).sublime-keymap
[
    { "keys": ["ctrl+shift+8"], "command": "convert_to_utf8", "args": {"encoding": "GBK", "stamp": "0" } }
]
\Packages\ConvertToUTF8\Default (OSX).sublime-keymap
[
    { "keys": ["super+shift+8"], "command": "convert_to_utf8", "args": {"encoding": "GBK", "stamp": "0" } }
]
\Packages\ConvertToUTF8\Default (Windows).sublime-keymap
[
    { "keys": ["ctrl+shift+8"], "command": "convert_to_utf8", "args": {"encoding": "GBK", "stamp": "0" } }
]
```

#### 四、 `Codecs​33` [🔗️ 895k](https://packagecontrol.io/packages/AutoFileName)
##### 1) 简介
由于 Sublime Text 3 内嵌的 Python 限制，ConvertToUTF8 可能无法正常工作。你可以安装本插件来解决这一问题
##### 2) 平台
`OS\LINUX`

#### 五、 `FileBrowser` [🔗️ 50k](https://packagecontrol.io/packages/FileBrowser)
##### 1) 简介
文件浏览树，可通过vim的操作方式创建删除选择文件
##### 2) 快捷键
在打开文件的情况下，`F1` 可直接打开当前文件所在目录树  
在目录树下面按 `P` 键，可查看自己预订义的 `jump_points`
##### 3) 偏好设置
此处的偏好设置
```
User\dired.sublime-settings
----------
{
    "color_scheme": "Packages/user/FileBrowser/dired.hidden-tmTheme",
    "dired_autorefresh": false,
    "dired_open_on_jump": true,
    "dired_jump_points":
    {
        "* BLOG *": "/Users/john/_Git/halfmoonvic.github.io/source/_posts/",
        "* FACE *": "/Users/john/Work/fe-face/",
        "* Git *": "/Users/john/_Git/",
        "* Go *": "/Users/john/Go/src/",
        "* HOME *": "/Users/john/",
        "* Nutstore *": "/Users/john/_Nutstore/",
        "* Plum *": "/Users/john/_Git/plum/",
        "* Rime *": "/Users/john/Library/Rime/",
        "* RimeSync *": "/Users/john/_Nutstore/Backups/RimeSync/",
        "* Subl *": "/Users/john/Library/Application Support/Sublime Text 3/Packages/User/",
        "* Sublime *": "/Users/john/_Nutstore/App/Sublime/"
    },
    "dired_to_copy":
    [
    ],
    "dired_to_move":
    [
    ]
}
```
##### 4) 主题色修改
###### 4.1 `PackageResourceViwer` 插件修改
可通过 安装 `PackageResourceViwer` 插件直接修改 `FileBrowser dired-rename-mode.hidden-tmTheme and dired.hidden-tmTheme` 的主题颜色。 
###### 4.2 偏好设置修改
1. 将修改好的 `dired-rename-mode.hidden-tmTheme` 和 `dired.hidden-tmTheme` 两个文件放置到 `User` 目录下面，
2. `User\dired.sublime-settings` 中的偏好设置已经设置好了普通模式下所采用的 `tmTheme` 文件。
3. `User\dired-rename-mode.sublime-settings` 中已经设置好了 修改名称模式下的 主题，直接将 `dired-rename-mode.sublime-settings` 文件放入到 `User\`目录下
###### 4.3 主题色文件 部分
```
<key>background</key>
<string>#282C34</string>         <!-- ### 更改背景色 -->
<key>foreground</key>
<string>#ABB2BF</string>         <!-- ### 文字颜色 -->
<key>gutter</key>
<string>#282C34</string>         <!-- ### 修改凹槽的背景色 -->
<key>selection</key>
<string>#1C4BDB</string>         <!-- ### 选中区域的颜色 -->
<key>selectionBorder</key>
<string>#1C4BDB</string>         <!-- ### 选中区域的颜色 -->
<key>background</key>
<string>#282C34</string>        <!-- ### 修改文件的背景色 -->
<key>foreground</key>
<string>#F09609</string>        <!-- ### 修改的文件 绿色 00c406 -->
<key>background</key>
<string>#282C34</string>        <!-- ### 未追踪的文件 的背景色 -->
<key>foreground</key>
<string>#FF0000</string>        <!-- ### 未追踪的文件 -->
```

#### 六、 `IMESupport` [🔗️ 219k](https://packagecontrol.io/packages/IMESupport)
##### 1) 简介
[[CJK] Handling IME message on Windows for Chinese,Korean,Japanese user.](https://github.com/SublimeTextIssues/Core/issues/468)  
sublime本身不支 持输入法跟随光标，作者做此插件  
上面作者未解决win10输入法跟随问题和新版小狼毫。国内达人fork其代码而制作的另外一份  
[Sublime Text 3 Windows 下拼音输入法支持](https://github.com/zcodes/IMESupport)

#### 七、 `FindKeyConflicts` [🔗️ 33k](https://packagecontrol.io/packages/FindKeyConflicts)
##### 1) 简介
找到冲突的 快捷键

#### 八、 `Local History` [🔗️ 57k](https://packagecontrol.io/packages/Local%20History)
##### 1) 简介
文件历时更改记录保留，只保留关闭文件时的文件状态

#### 九、 `PackageResourceViewer` [🔗️ 213k](https://packagecontrol.io/packages/PackageResourceViewer)
##### 1) 简介
1. Plugin to assist viewing and editing package resources in Sublime Text 3.
2. `Command Palette`命令下敲入 `package openresource` 后，可以迅速的查找更改插件的配置

#### 十、 `RawLineEdit` [🔗️ 15k](https://packagecontrol.io/packages/RawLineEdit)
##### 1) 简介
`Command Palette` 命令下敲入 rawlineedit toggle，显示换行符

#### 十一、 `SideBarEnhancements` [🔗️ 2M](https://packagecontrol.io/packages/SideBarEnhancements)
##### 1) 简介
侧边栏增强工具，右键在资源管理器中打开、新建文件、新建文件夹、以…打开、在浏览器中打开。
##### 2) 偏好设置
借助`Appserv` 可直接在 sublime 当中以 `localhost` 形式打开文件
https://my.oschina.net/u/925202/blog/542894
```
SideBarEnhancements.json
----------
{
    "C:\\Program Files (x86)\\AppServ\\www":{
        "url_testing":"http://localhost/",
        "url_production":"http://domain.tld/"
    },
    "experimental/":{
        "url_testing":"http://experimental/",
        "url_production":"http://domain.tld/"
    },
    "":{
        "url_testing":"http://the_url_for_the_project_root/",
        "url_production":"http://the_url_for_the_project_root/"
    }
}
```

#### 十二、 `Status Bar File Size` [🔗️ 5k](https://packagecontrol.io/packages/Status%20Bar%20File%20Size)
##### 1) 简介
状态栏显示文件大小

#### 十三、 `Sublimerge 3` 与 `Sublimerge Pro` [🔗️ 63k](https://packagecontrol.io/packages/Sublimerge%203) [🔗️ 131k](https://packagecontrol.io/packages/Sublimerge%20Pro)
##### 1) 简介
`Sublimerge 3` 免费 `Sublimerge Pro` 付费  
两者皆是文件差异比较工具，`Sublimerge Pro` 使用体验更佳

#### 十四、 `TabsExtra` [🔗️ 17k](https://packagecontrol.io/packages/TabsExtra)
##### 1) 简介
安装后右键单击Sublime里的文件标签，选clone就能在新标签中打开同一文件。  
配合Alt+Shift+2可以方便地分屏浏览

#### 十五、 `Terminal` [🔗️ 550k](https://packagecontrol.io/packages/Terminal)
##### 1) 简介
在 sublime 处 直接调出 cmd 等命令行
##### 2) 快捷键
##### 1. 去除默认快捷键
`Packages\Terminal` 目录下 去除默认快捷键  
```
Default (Linux).sublime-keymap
Default (OSX).sublime-keymap
Default (Windows).sublime-keymap
－－－－－－－－－－
[
    { "keys": [""], "command": "open_terminal" },
    { "keys": [""], "command": "open_terminal_project_folder" }
]
```
##### 2. 自定义快捷键
```
windows
{ "keys": ["ctrl+shift+e"], "command": "open_terminal" },
{ "keys": ["ctrl+shift+alt+e"], "command": "open_terminal_project_folder" },
mac 
{ "keys": ["super+shift+e"], "command": "open_terminal" },
{ "keys": ["ctrl+shift+super+e"], "command": "open_terminal_project_folder" },
```
##### 3) mac下偏好设置（mac无法直接打开iterm2）
1. 
```
{
    // "terminal": "C:\\WINDOWS\\system32\\cmd.exe",
    // "terminal": "C:\\Program Files\\cmder\\Cmder.exe",
    "terminal": "iTerm.sh",
    // "parameters": ["/START", "%CWD%"]
    "parameters": ["open_terminal_project_folder"]
}
```
2. packageOpenResource 找到 `iTerm.sh`，末尾添加如下代码
```
#!/bin/bash
CD_CMD="cd "\\\"$(pwd)\\\"" && clear"  
if echo "$SHELL" | grep -E "/fish$" &> /dev/null; then  
  CD_CMD="cd "\\\"$(pwd)\\\""; and clear"
fi  
VERSION=$(sw_vers -productVersion)  
OPEN_IN_TAB=0

while [ "$1" != "" ]; do  
    PARAM="$1"
    VALUE="$2"
    case "$PARAM" in
        --open-in-tab)
            OPEN_IN_TAB=1
            ;;
    esac
    shift
done

if (( $(expr $VERSION '<' 10.7) )); then  
    RUNNING=$(osascript<<END
    tell application "System Events"
        count(processes whose name is "iTerm")
    end tell
END  
)
else  
    RUNNING=1
fi

if (( ! $RUNNING )); then  
    osascript<<END
    tell application "iTerm"
            tell current window
                tell current session of (create tab with default profile)
                    write text "$CD_CMD"
                end tell
            end tell
            activate
    end tell
END  
else  
    if (( $OPEN_IN_TAB )); then
        osascript &>/dev/null <<EOF
        tell application "iTerm"
                    if (count of windows) = 0 then
                        set theWindow to (create window with default profile)
                        set theSession to current session of theWindow
                    else
                        set theWindow to current window
                        tell current window
                            set theTab to create tab with default profile
                            set theSession to current session of theTab
                        end tell
                    end if
                    tell theSession
                        write text "$CD_CMD"
                    end tell
                    activate
        end tell
EOF  
    else
        osascript &>/dev/null <<EOF
        tell application "iTerm"
                    tell (create window with default profile)
                        tell the current session
                            write text "$CD_CMD"
                        end tell
                    end tell
                    activate
        end tell
EOF  
    fi
fi  
```

#### 十六、 `Terminus` [🔗️ 7k](https://packagecontrol.io/packages/Terminus)
##### 1) 简介
向 vscode 那样调出terminal
##### 2) 快捷键
`super+grave`

#### 十六、 `BracketHighlighter` [🔗️ 1.72M](https://packagecontrol.io/packages/BracketHighlighter)
##### 1) 简介
1. 侧边栏成对出现相匹配的括号，放括弧花括弧等
2. HTML tag 标签成对高亮　　
##### 2) 偏好设置
1. `bh_core.sublime-settings` 当中记录了 不同 `{} － curly, () － round, [] － square, <> － angle, “” ” － quote` 对应不同的颜色设置
2. `bh_tag.sublime-settings` 则设置了tag匹配校验设置
3. 通过 PackageResourceViewer 插件 打开相应的主题文件 粘贴主题修改配置`tmThemeCode.json`中的内容  
    package openresource → monkai → Monokai - Spacegray Eighties.tmTheme
4. 主题修改配置 code 中的内容
有时候粘贴进去不生效的时候 等一下。进行 color theme  重新选择一下

#### 十七、 `EditorConfig` [🔗️ 163k](https://packagecontrol.io/packages/EditorConfig)
##### 1) 简介
1. 默认读取根目录下的 .editorconfig 配置，并且将会覆盖 sublime 默认的一些配置
2. 只有安装了此插件 才会 读取 根目录下的 `.editorconfig` 文件内容

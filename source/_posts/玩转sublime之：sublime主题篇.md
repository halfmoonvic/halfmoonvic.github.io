---
title: 玩转sublime之：sublime 主题修改篇
date: 2019-01-07 16:00:10
category: IDE
tags:
---

#### 一、 简介
`Sublime` 主题分为两种，一种是UI框架的修改，涉及字段为 "theme"。另外一种则是，各个语言高亮所使用的主题，涉及字段为 "color_scheme"

#### 二、 `A File Icon` [🔗️ 534k](https://packagecontrol.io/packages/A%20File%20Icon)
adds file specific icons to Sublime Text for improved visual grepping. It's heavily inspired by Atom File Icons

#### 三、 `Transparency` [🔗️ 12k](https://packagecontrol.io/packages/Transparency)
设置 sublime 透明度(原只可手动安装，名为 SublimeTextTrans)

#### 四、 各个主题
* `Colorsublime`,           
* `Theme - One Dark`,       
* `Solarized Color Scheme`, 
* `Monokai - Spacegray`,    
* `Visual Studio Dark`,     

#### 五、 sublime 默认主题修改
1. UI框架使用 sublime 默认的，即 `Default.sublime-theme`
2. 借助 `PackageResourceViewer` 插件修改 `Default.sublime-theme` 文件，以达到修改主题的目的
3. 修改是通过覆盖式的修改完成，故仅需将需要修改的代码在 `Default.sublime-theme` 最后粘贴即好，不需要特定的修改其中其中的配置
4. 部分修改使用了 `Theme - One Dark` 部分配置。所以，如果想要达到同样的效果，必须主动安装 `Theme - One Dark` 插件

```
// 自动补全 弹窗
{
    "class": "auto_complete",
    "layer0.tint": [40, 44, 42],                         // 自动补全背景色
    "layer0.opacity": 1.0,
    "row_padding": [7, 4],
    "dark_content": false
},
// Autocomplete - border
{
    "class": "popup_control",
    "content_margin": [0, 0],                            // [左右, 上下行间距]
    "layer0.tint": [31, 34, 40],
    "layer0.opacity": 1.0
},
// Autocomplete - label
{
    "class": "auto_complete_label",
    "fg": [155 ,163, 177],                                // 自动补全 非关键字的颜色
    "match_fg": [200, 203, 209],                          // 自动补全 关键字的颜色
    "selected_fg": [215, 216, 219],
    "selected_match_fg": [255, 255, 255]
},
// Autocomplete - label
{
    "class": "auto_complete_label",
    "fg": [155 ,163, 177],                                 // 自动补全 非关键字的颜色
    "match_fg": [200, 203, 209],                           // 自动补全 关键字的颜色
    "selected_fg": [215, 216, 219],                        // 自动补全当前激活状态所在项中 非关键字的颜色
    "selected_match_fg": [0, 203, 209]                     // 自动补全当前激活状态所在项中 关键字的颜色
},


// 侧边栏
{
    "class": "sidebar_container",
    "layer0.tint": [235, 237, 239],                        // 包裹侧边栏的 上下 背景色
    "layer0.tint": [41, 51, 61],                           // sidebar 深色背景的时候
    "layer0.opacity": 1.0,
    "content_margin": 0,
},
{
    "class": "sidebar_label",
    "color": [0, 0, 0],                                    // 文字颜色
    "color": [200, 200, 200],                              // sidebar 深色背景 文字颜色
    // "color": [157, 165, 180],                           // one dark
    "font.size": 13,                                       // mac 13 寸
    "font.size": 15,                                       // 1366 * 768
    "font.size": 17,                                       // 1920 * 1080 字体大小 - xiaoxin 13寸
    // "font.size": 19,                                       // 1920 * 1080 字体大小 - mi pro 15.6 寸 太大了，用上面的
    "font.bold": false,
    "font.italic": false,
    // "shadow_color": [250, 250, 250], "shadow_offset": [0, 0]
    // "font.face": "YaHei Consolas Hybrid"                // 添加的字体
},
{
    "class": "sidebar_tree",
    "row_padding": [16, 3, 16, 3],                         // 左间距 上下行间距
    // "row_padding": [7, 8],                              // one dark
    "indent": 20,                                          // 子文件缩进
    "indent_offset": 9,                                    // 左外侧的文件树缩进
    "indent_top_level": false,
    "layer0.tint": [235, 237, 239],                        // 侧边栏 背景色
     "layer0.tint": [41, 51, 61],                          // sidebar 深色背景  侧边栏 背景色
    // "layer0.tint": [33, 37, 43],                        // one dark
    "layer0.opacity": 1.0,
    "dark_content": false,
    "spacer_rows": true
},
{
    "class": "sidebar_tree",
    "platforms": ["windows"],
    "row_padding": [6, 4, 6, 4],                           // 1366 * 768
    "row_padding": [6, 6, 6, 7],                           // 1920 * 1080 mi-pro
},

// sidebar 深色背景的时候 用
{
    "class": "tree_row",
    "attributes": ["selectable", "hover"],
    "layer0.tint": [69, 74, 85],
    "layer0.opacity": 1,
},
{
    "class": "tree_row",
    "attributes": ["selected"],
    "layer0.tint": [62, 62, 62],
    "layer0.opacity": 1.0,
},
{
    "class": "sidebar_heading",
    "fg": [200, 200, 200],
    "font.size": 13,
    "font.bold": true,
    "shadow_color": [200, 200, 200, 0.36],
    "shadow_offset": [0, 1]
},

// tab 标题栏
{
    "class": "tabset_control",
    "layer0.opacity": 1.0,
    "tint_index": 0,                                                             // 无标题栏处的透明度
    // "layer1.texture": "Theme - Default/tabset_background_transparent.png",
    // "layer0.texture": "Theme - One Dark/assets/tab_panel_bg.png",             // one dark
    "layer1.inner_margin": [2, 6],
    "layer1.opacity": 0,
    "content_margin": [4, 0, 8, 0],                            // 标题栏距离上下左右距离
    "tab_overlap": 17,
    "tab_width": 180,                                          // 宽度
    "tab_min_width": 48,                                       // 最小宽度
    "tab_height": 35,                                          // 高度
    // "tab_width": 254,                                       // one dark
    // "tab_min_width": 107,                                   // one dark
    // "tab_height": 42,                                       // one dark
    "mouse_wheel_switch": false,
},
{
    "class": "tab_label",
    // "fg": [0, 21, 201, 255],                                   // 未激活态的标签的字体颜色 所有的，包含markdown
    "fg": [255, 255, 0, 255],                               // one dark
    "shadow_color": [0, 0, 0, 0],
    "shadow_offset": [0, 0],
    "font.italic": false,
    "font.size": 13,                                            // 字体大小 mac 13 寸
    "font.size": 15,                                            // 字体大小
},
{
    "class": "tab_label",
    "parents": [{"class": "tab_control", "attributes": ["file_medium_dark"]}],
    "fg": [200, 200, 200, 255],                                // 未激活态的标签的字体颜色， 就是markdown
    // "fg": [255, 255, 255, 255],                             // one dark
    "shadow_color": [0, 0, 0, 100],
    "shadow_offset": [0, -1]
},
{
    "class": "tab_label",
    "parents": [{"class": "tab_control", "attributes": ["selected", "file_medium_dark"]}],
    "fg": [0, 255, 255, 230],                                  // 激活态的标签的字体颜色
    "shadow_color": [0, 0, 0, 255],
    "shadow_offset": [0, -1]
},
{
    "class": "tab_label",
    "parents": [{"class": "tab_control", "attributes": ["file_light"]}],
    "attributes": ["dirty"],
    "settings": ["highlight_modified_tabs"],
    "fg": [255, 23, 0]                                         // markdown 文档修改后的颜色
},
{
    "class": "tab_label",
    "parents": [{"class": "tab_control", "attributes": ["file_medium"]}],
    "attributes": ["dirty"],
    "settings": ["highlight_modified_tabs"],
    "fg": [255, 23, 0]
},
{
    "class": "tab_label",
    "parents": [{"class": "tab_control", "attributes": ["file_medium_dark"]}],
    "attributes": ["dirty"],
    "settings": ["highlight_modified_tabs"],
    "fg": [255, 161, 52]                                       // 修改文件后的标题栏的高亮色
},
{
    "class": "tab_label",
    "parents": [{"class": "tab_control", "attributes": ["file_dark"]}],
    "attributes": ["dirty"],
    "settings": ["highlight_modified_tabs"],
    "fg": [255, 161, 52]
},

// tab 标题栏 Tooltip - background
{
    "class": "tool_tip_control",
    "content_margin": [10, 10],
    "layer0.texture": "Theme - One Dark/assets/tooltip_bg.png",     // 标题栏hover效果时，浮现的路径主题（那个背景图片）
    "layer0.inner_margin": [3, 3],
    "layer0.opacity": 1.0
},
// tab 标题栏 Tooltip - label
{
    "class": "tool_tip_label_control",
    "color": [255, 255, 255],                                  // 里面的文字颜色
    "font.size": 15,                                           // 里面的文字大小
    "font.size": 13,                                           // mac 13 寸
},

// 状态栏
{
    "class": "status_bar",
    // "layer0.texture": "Theme - Default/status_bar.png",                       // 状态栏背景色（图片）
    // "layer0.texture": "Theme - One Dark/assets/find_panel_bg.png",            // one dark
    "layer0.tint": [40, 44, 52],                               // 背景色 即是视图区域的背景色
    "layer0.tint": [28, 31, 36],                               // 背景色
    "layer0.opacity": 1.0,                                     // 状态栏背景透明度
    "layer0.inner_margin": [2, 2],
    "content_margin": [8, 0, 8, 0]                             // 左上右下
},

// 状态栏 右下角 文字信息
{
    "class": "status_button",
    "layer0.tint": [125, 125, 125],                            // 线的颜色
    "layer0.opacity": 0,                                       // 线的透明度
    "layer0.draw_center": false,
    "layer0.inner_margin": [1, 0, 0, 0],
    "content_margin": [10, 2, 10, 3],
    "min_size": [80, 0]                                        // [文字左右间隔, 行高]
},
{
    "class": "label_control",
    "parents": [{"class": "status_bar"}],
    "fg": ["background", 255, 255, 255, 0.7],                  // 状态栏左侧文字颜色
    "font.size": 13,                                           // 状态栏上文字大小  1366 * 768  && mac 13 寸
    "font.size": 16,                                           // 状态栏上文字大小
    "shadow_color": [0, 0, 0, 0.18],
    "shadow_offset": [0, 1]
},

// Panels 搜索等的弹出框
{
    "class": "panel_control",
    "layer0.tint": [199, 203, 209],
    "layer0.opacity": 1.0,
    "content_margin": 0                                         // 整体控制 两侧 与 上下的留白
},
{
    "class": "panel_grid_control",
    "inside_spacing": 4,                                        // 几个按钮之间距离
    "outside_hspacing": 0,                                      // 两侧留白
    "outside_vspacing": 1                                       // 上下留白
},
```

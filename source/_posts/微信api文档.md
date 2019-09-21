---
title: 微信api文档
date: 2018-05-27 15:58:27
category: api
tags:
---
[微信小程序文档](https://developers.weixin.qq.com/miniprogram/dev/framework/MINA.html)

### 一、 框架 → 配置 
主要介绍 app.json 与 page.json 控制的各项内容  

1. app.json 包含 
    * pages(页面路径列表) 
    * window(全局的默认窗口表现——状态栏、导航条、标题、窗口背景色) 
    * tabBar(底部 tab 栏的表现) 
    * networkTimeout(网络超时时间) 
    * debug(是否开启debug模式，默认关) 
    * functionalPages(是否启用插件功能页，默认关， 18/08/04 添加的) 
    * subPackages(分包结构配置， 18/08/04 添加的) 
    * workers(Worker 代码放置的目录， 18/08/04 添加的)  
2. page.json 针对当前 page，对 app.json 里面的 window 选项进行覆盖式配置

### 二、 框架 → 逻辑层
1. 增加 App 和 Page 方法，进行程序和页面的注册。
2. 增加 getApp 和 getCurrentPages 方法，分别用来获取 App 实例和当前页面栈。
3. 提供丰富的 API，如微信用户数据，扫一扫，支付等微信特有能力。
4. 每个页面有独立的作用域，并提供模块化能力。
5. 由于框架并非运行在浏览器中，所以 JavaScript 在 web 中一些能力都无法使用，如 document，window 等。
6. 开发者写的所有代码最终将会打包成一份 JavaScript，并在小程序启动的时候运行，直到小程序销毁。类似 ServiceWorker，所以逻辑层也称之为 App Service。

#### 1）注册程序 `App()` ----- app.js
##### 1. object参数说明
App() 函数用来注册一个小程序。接受一个 **object参数** (下表)，其指定小程序的生命周期函数等。

|属性|类型|描述|触发时机|
|:--|:--|:--|:--|
|onLaunch|Function|生命周期函数|监听小程序初始化——当小程序初始化完成时，会触发 onLaunch（全局只触发一次）
|onShow|Function|生命周期函数|监听小程序显示——当小程序启动，或从后台进入前台显示，会触发 onShow
|onHide|Function|生命周期函数|监听小程序隐藏——当小程序从前台进入后台，会触发 onHide
|onError|Function|错误监听函数|当小程序发生脚本错误，或者 api 调用失败时，会触发 onError 并带上错误信息
|onPageNotFount|Function|页面不存在监听函数|当小程序出现要打开的页面不存在的情况，会带上页面信息回调改函数
|其他|Any||开发者可以添加任意的函数或数据到 Object 参数中，用 this 可以访问

##### 2. getApp()
全局的 getApp() 函数可以用来获取到小程序实例
```
var appInstance = getApp()
console.log(appInstance.globalData) // I am global data
```

#### 2) 注册页面 `Page()`  ------ pageXXX.js
##### 1.  object参数说明
Page(Object) 函数用来注册一个页面。接受一个 Object 类型参数，其指定页面的初始数据、生命周期函数、事件处理函数等。

|属性|类型|描述|触发时机|
|:--|:--|:--|:--|
|data|Object|页面的初始数据||
|onLoad|Function|<span style="color:red">生命周期回调</span>|监听页面加载|
|onShow|Function|<span style="color:red">生命周期回调</span>|监听页面显示|
|onReady|Function|<span style="color:red">生命周期回调</span>|一个页面只会调用一次，代表页面已经准备妥当，可以和视图层进行交互。
|onHide|Function|<span style="color:red">生命周期回调</span>|监听页面隐藏。当navigateTo或底部tab切换时调用|
|onUnload|Function|<span style="color:red">生命周期回调</span>|监听页面卸载。 当redirectTo或navigateBack的时候调用。|
|onPullDownRefresh|Function|页面相关时间处理|监听用户下拉动作|
|onReachBottom|Function|页面相关时间处理|页面上拉触底事件的处理函数|
|onShareAppMessage|Function|页面相关时间处理|用户转发|
|onPageScroll|Function|页面相关时间处理|页面滚动触发事件的处理函数|
|onTabItemTap|Function|页面相关时间处理|当前是 tab 页时，点击 tab 时触发|
|其它|Any||开发者可以添加任意的函数或数据到 Object 参数中，在页面的函数中用 this 可以访问|

##### 2. 页面事件处理函数  
1. **`onPullDownRefresh`**: 页面相关事件处理函数--监听用户下拉动作，下拉刷新    
    * 需要在`app.json`的`window`选项中或页面配置中开启`enablePullDownRefresh`。
    * 可以通过`wx.startPullDownRefresh`触发下拉刷新，调用后触发下拉刷新动画，效果与用户手动下拉刷新一致。
    * 当处理完数据刷新后，`wx.stopPullDownRefresh`可以停止当前页面的下拉刷新。
2. **`onReachBottom`**: 监听用户上拉触底事件。
    * 可以在`app.json`的`window`选项中或页面配置中设置触发距离`onReachBottomDistance`。 
    * 在触发距离内滑动期间，本事件只会被触发一次。 
3. **`onPageScroll(Object)`**: 监听用户滑动页面事件。
    * 参数为 Object，包含以下字段： `scrollTop 页面在垂直方向已滚动的距离（单位px）
`
4. **`onShareAppMessage(Object)`**: 用户转发
    * 只有定义了此事件处理函数，右上角菜单才会显示“转发”按钮
    * 用户点击转发按钮的时候会调用
    * 此事件需要 return 一个 Object，用于自定义转发内容
        + title 转发标题    当前小程序名称 
        + path    转发路径    当前页面 path ，必须是以 / 开头的完整路径 
        + imageUrl  自定义图片路径，可以是本地文件路径、代码包文件路径或者网络图片路径。支持PNG及JPG。显示图片长宽比是 5:4。
5. **`onTabItemTap`**: 当前是 tab 页时，点击 tab 时触发
6. **`Page.prototype.route`**: route 字段可以获取到当前页面的路径。
7. **`Page.prototype.setData()`**: setData 函数用于将数据从逻辑层发送到视图层（异步），同时改变对应的 this.data 的值（同步）。

#### 3） 框架 → 路由
在小程序中所有页面的路由全部由框架进行管理。框架以栈的形式维护了当前的所有页面。 当发生路由切换的时候，页面栈的表现如下：

##### 1. 页面栈
|路由方式|页面栈表现|
|:--|:--|
|初始化|新页面入栈|
|打开新页面|新页面入栈|
|页面重定向|当前页面出栈，新页面入栈|
|页面返回|页面不断出栈，直到目标返回页，新页面入栈|
|Tab 切换|页面全部出栈，只留下新的 Tab页面|
|重加载|页面全部出栈，只留下新的页面|

#####　2. getCurrentPages()
getCurrentPages() 函数用于获取当前页面栈的实例，以数组形式按栈的顺序给出，第一个元素为首页，最后一个元素为当前页面。

##### 3. 路由方式
|路由方式|触发时机|路由前页面|路由后页面|
|:--|:--|:--|:--|
|初始化|小程序打开的第一个页面||onLoad, onShow|
|打开新页面|调用 `API wx.navigateTo` 或使用组件 `<navigator open-type="navigateTo"/>`|onHide|onLoad, onShow|
|页面重定向|调用 `API wx.redirectTo` 或使用组件 `<navigator open-type="redirectTo"/>`|onUnload|onLoad, onShow|
|页面返回|调用 `API wx.navigateBac`k 或使用组件`<navigator open-type="navigateBack">`或用户按左上角返回按钮|onUnload|onShow|
|Tab 切换|调用 `API wx.switchTab` 或使用组件 `<navigator open-type="switchTab"/>` 或用户切换 Tab||各种情况请参考下表|
|重启动|调用 `API wx.reLaunch` 或使用组件 `<navigator open-type="reLaunch"/>`|onUnload|onLoad, onShow|





---
title: 微信api文档
date: 2018-05-27 15:58:27
category: api
tags:
---
### 一、 框架 → 配置 
主要介绍 app.json 与 page.json 控制的各项内容  
1. app.json 包含 `pages window tabBar networkTimeout debug` 五项内容
2. page.json 针对当前 page，对 app.json 里面的 window 选项进行覆盖式配置

### 二、 框架 → 逻辑层
1. 增加 App 和 Page 方法，进行程序和页面的注册。
2. 增加 getApp 和 getCurrentPages 方法，分别用来获取 App 实例和当前页面栈。
3. 提供丰富的 API，如微信用户数据，扫一扫，支付等微信特有能力。
4. 每个页面有独立的作用域，并提供模块化能力。
5. 由于框架并非运行在浏览器中，所以 JavaScript 在 web 中一些能力都无法使用，如 document，window 等。
6. 开发者写的所有代码最终将会打包成一份 JavaScript，并在小程序启动的时候运行，直到小程序销毁。类似 ServiceWorker，所以逻辑层也称之为 App Service。

#### 1）注册程序 `App()`
1. onLaunch：生命周期函数——监听小程序初始化——当小程序初始化完成时，会触发 onLaunch（全局只触发一次）
2. onShow：生命周期函数——监听小程序显示——当小程序启动，或从后台进入前台显示，会触发 onShow
3. onHide：生命周期函数——监听小程序隐藏——当小程序从前台进入后台，会触发 onHide
4. onError：错误监听函数——当小程序发生脚本错误，或者 api 调用失败时，会触发 onError 并带上错误信息
5. getApp()： 全局的 getApp() 函数可以用来获取到小程序实例。
```
var appInstance = getApp()
console.log(appInstance.globalData) // I am global data
```

#### 2) 注册页面 `Page()` 
Page()函数用来注册一个页面。接受一个 object 参数，其指定页面的初始数据、生命周期函数、事件处理函数等。

##### 生命周期函数
1. onLoad: 页面加载。 一个页面只会调用一次，可以在 onLoad 中获取打开当前页面所调用的 query 参数。
2. onShow: 页面显示。 每次打开页面都会调用一次。
3. onReady: 页面初次渲染完成。 一个页面只会调用一次，代表页面已经准备妥当，可以和视图层进行交互。
对界面的设置如wx.setNavigationBarTitle请在onReady之后设置。详见生命周期
4. onHide: 页面隐藏。 当navigateTo或底部tab切换时调用。
5. onUnload: 页面卸载。 当redirectTo或navigateBack的时候调用。

##### 其它的页面内勾子函数
1. onPullDownRefresh: 页面相关事件处理函数--监听用户下拉动作，下拉刷新
    *　监听用户下拉刷新事件。 
    *　需要在app.json的window选项中或页面配置中开启enablePullDownRefresh。 
    *　当处理完数据刷新后，wx.stopPullDownRefresh可以停止当前页面的下拉刷新。
2. onReachBottom: 上拉触底
    * 监听用户上拉触底事件。 
    * 可以在app.json的window选项中或页面配置中设置触发距离onReachBottomDistance。 
    * 在触发距离内滑动期间，本事件只会被触发一次。 
3. onPageScroll: 页面滚动
    * 监听用户滑动页面事件。 
    * 参数为 Object，包含以下字段： `scrollTop 页面在垂直方向已滚动的距离（单位px）
`
4. onShareAppMessage: 用户转发
    * 只有定义了此事件处理函数，右上角菜单才会显示“转发”按钮
    * 用户点击转发按钮的时候会调用
    * 此事件需要 return 一个 Object，用于自定义转发内容
        + title 转发标题    当前小程序名称 
        + path    转发路径    当前页面 path ，必须是以 / 开头的完整路径 
5. onTabItemTap: 当前是 tab 页时，点击 tab 时触发
6. Page.prototype.route: route 字段可以获取到当前页面的路径。
7. Page.prototype.setData(): setData 函数用于将数据从逻辑层发送到视图层（异步），同时改变对应的 this.data 的值（同步）。

#### 3） 框架 → 路由
在小程序中所有页面的路由全部由框架进行管理。框架以栈的形式维护了当前的所有页面。 当发生路由切换的时候，页面栈的表现如下：

##### 页面栈
|路由方式|页面栈表现|
|:--|:--|
|初始化|新页面入栈|
|打开新页面|新页面入栈|
|页面重定向|当前页面出栈，新页面入栈|
|页面返回|页面不断出栈，直到目标返回页，新页面入栈|
|Tab 切换|页面全部出栈，只留下新的 Tab页面|
|重加载|页面全部出栈，只留下新的页面|

#####　getCurrentPages()
getCurrentPages() 函数用于获取当前页面栈的实例，以数组形式按栈的顺序给出，第一个元素为首页，最后一个元素为当前页面。

##### 路由方式
|路由方式|触发时机|路由前页面|路由后页面|
|:--|:--|:--|:--|
|初始化|小程序打开的第一个页面||onLoad, onShow|
|打开新页面|调用 `API wx.navigateTo` 或使用组件 `<navigator open-type="navigateTo"/>`|onHide|onLoad, onShow|
|页面重定向|调用 `API wx.redirectTo` 或使用组件 `<navigator open-type="redirectTo"/>`|onUnload|onLoad, onShow|
|页面返回|调用 `API wx.navigateBac`k 或使用组件`<navigator open-type="navigateBack">`或用户按左上角返回按钮|onUnload|onShow|
|Tab 切换|调用 `API wx.switchTab` 或使用组件 `<navigator open-type="switchTab"/>` 或用户切换 Tab||各种情况请参考下表|
|重启动|调用 `API wx.reLaunch` 或使用组件 `<navigator open-type="reLaunch"/>`|onUnload|onLoad, onShow|





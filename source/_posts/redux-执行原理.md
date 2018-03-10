---
title: react-redux 执行原理
date: 2018-03-10 09:15:30
category: sorce
tags: [redux]
---

示例代码如下：
```
index.js 文件
const store = createStore(counter, applyMiddleware(thunk))

ReactDom.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)

＝＝＝＝＝＝＝＝＝
app.js 文件

import React from 'react'

import { connect } from './mini-redux/mini-react-redux.js'
import { setAdd, setRemove, getAdd, getRemove } from './mini-redux/test-miniredux.js'

class App extends React.Component {
  render() {
    return (
      <div>
        <h2>现在的数值是 {this.props.num}</h2>
        <button onClick={() => this.props.setAdd(2)}>加</button>
        <button onClick={() => this.props.setRemove(3)}>减</button>
        <button onClick={() => this.props.getAdd(3)}>过1秒加3</button>
        <button onClick={() => this.props.getRemove(2)}>过1秒减2</button>
      </div>
    )
  }
}

App = connect(
  state => ({ num: state }), { setAdd, setRemove, getAdd, getRemove }
)(App)

export default App
```

### 执行步骤
1. 执行 index.js 文件中的代码。
2. 通过 `const store = createStore(counter, applyMiddleware(thunk))` 初始化 redux。其中 createStore 接受 reducer 与 中间件。中间件暂且不表。
3. createStore 函数返回 `{ getState, subscribe, dispatch }` 对象.  
    * `getState` 函数 返回 `currentState`
    * `subscribe` 函数 负责监听 `currentState`。当调用 `dispatch` 来更改 `currentState`的时候会执行 `subscribe` 中收集的函数。用于更新组件
    * `dispatch` 接受 `reducer`。通过 `reducer(actionCreator)`方式更改 `currentState`
4. 通过 `<Provider store={store}><App /></Provider>,` 挂载具体数据  
    * 将 `createStore` 的返回值`store` 挂载到 `<Provider>` 组件上
    * `<Provider>` 组件内部将 `store` 数据声明为全局可引用的 `getChildContext`
5. 执行 app.js 文件中的代码
6. 通过 `App = connect(state => ({ num: state }), { setAdd, setRemove, getAdd, getRemove } )(App)` 来重写 `App` 组件。
7. `connect` 接受两个参数。  
    * 第一个参数为 `mapStateToProps` 一个匿名函数，
    * 第二个参数则是我们事先声明好的 `action creator`。  
    * connect作用负责将 `<Provider>` 上传递过来的`store`放到使用 `connect` 组件的 `props`上面
8. `connect` 运作原理  
    * 声明一个 `tempProps` 临时对象
    * 首先通过 `this.context` 拿到 `store`。
    * 在通过 `store.getState()`拿到 `stateProps` 数据后，将其放置到 临时对象 `tempProps` 上面。
    * 在自性制作一个 `bindActionCreators(actionCreators, store.dispatch)` 函数。
    * 将各个 `actionCreator` 用 `store.dispatch` 函数包裹住。
    * 最后`bindActionCreators`返回一个对象，其 `key` 值为各个 `actionCreator` 的名字。 `value` 值为 `store.dispatch(actionCreator())`。
    * 然后将此对象通过对象扩展运算符的方式加入到 `tempProps` 临时对象上
    * 最后将此临时对象挂载到 新的 `<App>` 组件上
    * 另外通过 `store.subscribe` 监控 临时对象 `tempProps` 的改变。以便在 `currentState` 发生变化的时候,实时更新 `tempProps` 对象上的 `stateProps`，因为用户使用了 `stateProps` 数据，它的变化继而可以引发视图的更新
9. 用户点击执行过程.........
10. 用户点击 `加`。 执行`this.props.setAdd(2)`语句
11. 第8部讲过 `actionCreator` 已经被 `dispatch` 包裹住了。  
    * 实际上执行的语句为 `store.dispatch(actionCreator())`。 
    * `dispatch` 函数内部通过`reducer(actionCreator)`方式来更新`currentState`值。 
    * `currentState`发生改变后上面的`tempProps`临时对象跟随改变。通过 `props` 方式挂载到 `<App>` 上面的值发生更改。`<App>` 组件中的`this.props.num`更改，视图自动更新！

#### 中间件
1. 如果传入了中间件。那么 `createStore` 将作为参数传入`applyMiddleware`。
2. `getState` 无变化。重点是 `dispatch`。
3. `applyMiddleware` 内部 调用 `middleware` 中间件来重写 `dispatch` 方法
4. 即将用户传入的异步函数在中间件处。并且将`dispatch, getState`作为参数传给异步函数。使异步函数可在内部调用 `dispatch(actionCreator)`。
5. 其余与上述基本一致

#### 源码
[react-boss](https://github.com/halfmoonvic/React-Boss)  
下载至本地后查看 redux 分支。里面有具体的注释说明

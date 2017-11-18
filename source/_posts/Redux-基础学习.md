---
title: Redux 基础学习
date: 2017-11-18 18:25:18
tags: [redux, vuex]
---

### 一、手动触发 dispatch

**index.redux.js**
```
const ADD_TYPE = 'increase'
const REMOGE_TYPE = 'decrease'

// action creator
export function addCounter(val = 1) {
  return { type: ADD_TYPE, payload: val }
}
export function removeCounter(val = 1) {
  return { type: REMOGE_TYPE, payload: val }
}

// reducer
export function counter(state = 0, action) {
  switch (action.type) {
    case ADD_TYPE:
      return state + action.payload
    case REMOGE_TYPE:
      return state - action.payload
    default:
      return 0
  }
}
```

**index.js**
```
import React from 'react'
import ReactDom from 'react-dom'

import { createStore } from 'redux'
import { counter, addCounter, removeCounter } from './index.redux.js'

import App from './app'

const store = createStore(
  counter,
  window.devToolsExtension ? window.devToolsExtension() : ()=>{}   // 使谷歌扩展redux插件能够记录变化
)

function render() {
  ReactDom.render(
    <App store={store} propAdd={addCounter} propRemove={removeCounter} />,
    document.getElementById('root')
  )
}
render()

store.subscribe(render)
```

**app.js**
```
import React from 'react'

class App extends React.Component {
  render() {
    const store = this.props.store

    console.log('渲染ing')
    console.log(this.props)
    console.log(store.getState())
    return (
      <div>
        <h1>总数：{store.getState()}</h1>
        <button onClick={() => store.dispatch(this.props.propAdd(2))}>加</button>
        <button onClick={() => store.dispatch(this.props.propRemove(2))}>减</button>
      </div>
    )
  }
}

export default App
```

#### 执行过程：
###### 1) index.redux.js
index.redux.js 文件中声明好各个 action(返回对象，描述发生的事情) 与 reducer（处理action当中的描述，针对action做出回应，生成新的state）
###### 2) index.js
1. index.js 当中引入 reducer，通过 createStore(reducer) 来生成 store 实例。
2. 引入 各个action，并作为 prop 属性挂载到组件实例上面，方便以后实例直接通过 this.props.XXX 来获取 action
###### 3) app.js
手动执行触发 dispatch。  
store.dispatch 可自行触发 reducer 的执行。 propsAdd() 执行后，也就是 index.redux.js 当中的 action函数执行后，返回的是action对象{type: ADD_TYPE}。dispatch将action传给 reducer，执行更新

![this.props输出结果](/images/2017-11-18-redux/dispatch.jpg)
上图为 this.props 和 store.getState() 输出结果

-------------

-------------

### 拆分reducer
###### 1) store.getState()
此函数即是获取 reducer 的返回值！

**index.redux.js**
```
import { combineReducers } from 'redux';

const ADD_TYPE = 'increase'
const REMOGE_TYPE = 'decrease'
const CHENG_TYPE = 'cheng'
const CHU_TYPE = 'chu'

// action creator
export function addCounter(val = 1) {
  return { type: ADD_TYPE, payload: val }
}
export function removeCounter(val = 1) {
  return { type: REMOGE_TYPE, payload: val }
}
export function chengCounter(val = 1) {
  return { type: CHENG_TYPE, payload: val }
}
export function chuCounter(val = 1) {
  return { type: CHU_TYPE, payload: val }
}


// reducer
export function addRemoveCounter(state, action) {
  switch (action.type) {
    case ADD_TYPE:
      return state + action.payload
    case REMOGE_TYPE:
      return state - action.payload
    default:
      return 0
  }
}
// reducer
export function chengChuCounter(state, action) {
  switch (action.type) {
    case CHENG_TYPE:
      return state * action.payload
    case CHU_TYPE:
      return state / action.payload
    default:
      return 1
  }
}

/**
 * reducers 手动合并
 */
// export function counter(state = 1, action) {
//   return {
//     addRemoveCounter: addRemoveCounter(state.addRemoveCounter, action),
//     chengChuCounter: chengChuCounter(state.chengChuCounter, action)
//   }
// }
// redux 方法的 combineReducers 结果与上面的书写的函数是一样一样的
/**
 * 利用 redux 的 combineReducers 方法进行合并
 * 效果与上面是一样的
 */
export const counter = combineReducers({
  addRemoveCounter,
  chengChuCounter
})

```

**index.js**
```
import React from 'react'
import ReactDom from 'react-dom'

import { createStore } from 'redux'
import { counter, addCounter, removeCounter, chengCounter, chuCounter } from './index.redux.js'

import App from './app'

const store = createStore(
  counter,
  window.devToolsExtension ? window.devToolsExtension() : ()=>{}   // 使谷歌扩展redux插件能够记录变化
)

function render() {
  ReactDom.render(
    <App store={store} propAdd={addCounter} propRemove={removeCounter} propCheng={chengCounter} propChu={chuCounter} />,
    document.getElementById('root')
  )
}
render()

store.subscribe(render)
```

**app.js**
```
import React from 'react'

class App extends React.Component {
  render() {
    const store = this.props.store
    console.log('渲染ing')
    console.log(this.props)
    console.log(store.getState())
    return (
      <div>
        <h1>加减state：{store.getState().addRemoveCounter}</h1>
        <h1>乘除state：{store.getState().chengChuCounter}</h1>
        <button onClick={() => store.dispatch(this.props.propAdd(2))}>加</button>
        <button onClick={() => store.dispatch(this.props.propRemove(2))}>减</button>
        <button onClick={() => store.dispatch(this.props.propCheng(2))}>乘</button>
        <button onClick={() => store.dispatch(this.props.propChu(2))}>除</button>
      </div>
    )
  }
}

export default App
```

#### 执行过程：
###### 1) index.redux.js
1. index.redux.js 文件中声明好各个 action(返回对象，描述发生的事情) 
2. 声明好子reducer，手动或者通过 combineReducers 来将各个子 reducer 合并成一个 大的reducer。
3. 仅记一点，store.getState() 即是 这个大 reducer的返回值
4. 各个 子reducer 是不同的 state 相互之间没关系
###### 2) index.js
1. index.js 当中引入 reducer，通过 createStore(reducer) 来生成 store 实例。
2. 引入 各个action，并作为 prop 属性挂载到组件实例上面，方便以后实例直接通过 this.props.XXX 来获取 action
###### 3) app.js
1. 状态展现要依据 store.getState() 的值 即 {addRemoveCounter: 0, chengChuCounter: 1}，故一开始是声明了两个 state 状态
2. 手动执行触发 dispatch。
store.dispatch 可自行触发 reducer 的执行。 propsAdd() 执行后，也就是 index.redux.js 当中的 action函数执行后，返回的是action对象{type: ADD_TYPE}。dispatch将action传给 reducer，执行更新

![this.props输出结果](/images/2017-11-18-redux/connect.jpg)
上图为 this.props 输出结果

----------------------

----------------------

### 异步的 action
action 必须是一个 plain object, 需要一些插件的支持
```
import thunk from 'redux-thunk'
import { createStore, applyMiddleware, compose } from 'redux'

创建 store 
const store = createStore(
  counter,
  compose(
    applyMiddleware(thunk),
    window.devToolsExtension ? window.devToolsExtension() : ()=>{}
  )
)

// action creator
export function addCounter(val = 1) {
  return dispatch => dispatch({ type: ADD_TYPE, payload: val })
}
export function removeCounter(val = 1) {
  return dispatch => {
    setTimeout(() => {
      dispatch({ type: REMOGE_TYPE, payload: val })
    }, 2000)
  }
}
```

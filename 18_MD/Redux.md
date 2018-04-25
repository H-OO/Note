## Redux

Redux 是一种架构，将 Flux 架构与函数式编程结合在一起；用于解决 React 不能开发大型复杂应用的问题。

---

**何时使用**

从应用角度看

```
用户的使用方式复杂
不同身份的用户有不同的使用方式（比如普通用户和管理员）
多个用户之间可以协作
与服务器大量交互，或者使用了WebSocket
View要从多个来源获取数据
```

从组件角度看

```
某个组件的状态，需要共享
某个状态需要在任何地方都可以拿到
一个组件需要改变全局状态
一个组件需要改变另一个组件的状态
```

---

**基本概念和 API**

store 保存数据的地方，是一个对象

```
import { createStore } from 'redux';
const store = createStore(fn);
```

state 当前时刻的数据集合，一个 state 对应一个 View

```
import { createStore } from 'redux';
const store = createStore(fn); // reducer函数
const state = store.getState();
```

action View 发出的通知，表示 state 需要发生改变了

```
const action = {
  type: 'ADD_TODO', // 触发ADD_TODO的行为
  payload: 'Learn Redux' // 携带的信息
};
```

store.dispatch() 是 View 发出 action 的唯一方法

```
import { createStore } from 'redux';
const store = createStore(fn);
store.dispatch({
  type: 'ADD_TODO',
  payload: 'Learn Redux'
});
```

reducer 函数 是 createStore 的参数，是一个纯函数，用于获取[全新]的 state，使 View 更新

```
const reducer = (state, action) => {
  switch(action.type) {
    case 'ADD_TODO':
      // ...
      return new_state
      break;
  }
}
```

store.subscribe() 订阅 state 的变化，触发 View 更新

```
let unsubscribe = store.subscribe(() =>
  console.log(store.getState())
  // ...
);
unsubscribe(); // 取消订阅
```

---

**同步渲染**

简单的状态改变流程：  
View → Action → Dispatch → Store → State → View

人话：  
用户在 View(视图)上交互触发生成 Action(信息对象)，Action 通过 Dispatch(派发器)传递给 Store(状态仓库)，  
Store 修改状态后回馈给了 State(组件 State)，State 的改变会使 React 重新调用 render 方法渲染视图

细节剖析：  
1.Dispatch 传递给 Store  
首先，Store 的创建需要使用到一个 Reducers 函数(不同的 Action 有不同的处理方式，由它来提供匹配处理方式)，  
这样一来 Store 就能对 Dispatch 传递过来的 Action 进行匹配(switch-case)，选取出对应的处理方式后将处理的结果返回，  
返回的结果会被 Store 内部方法接收，将对应的旧 State(Store 中的 State)进行替换
Reducers 函数 (oldState, action)=>{}  
2.Store 修改 State  
Store 中的状态发生改变后会反馈给 State(组件 State)。怎么实现的？  
这个反馈过程其实就是执行订阅的回调函数。怎么说？  
具体：  
使用组件 componentDidMount 钩子函数(DOM 完成挂载)，通过 store.subscribe 函数订阅 Store 中状态的改变，  
订阅函数的参数为 Store 发生状态修改后执行的回调函数  
这个回调函数通过重新设置组件中的状态`this.setState`来重新调用 render 方法渲染视图

---

**异步渲染**

作用：获取服务器端的数据进行视图渲染

思路：需要使用 Redux 中间件`redux-thunk`对 Dispatch 内部进行改造，使其可传入函数作为参数（默认只能传入对象）

依赖：`redux-thunk`

异步渲染流程：  
View → Action → Dispatch → Store → State → View → Action → Dispatch → Store → State → View

具体实现（有点抽象）：  
从创建 Store 开始，createStore 方法需要两个参数，分别是 reducers 函数 和 applyMiddleware 方法执行返回的结果
applyMiddleware 方法的作用是将传入的中间件已数组形式进行保存，让 createStore 方法内部依次执行中间件  
这里我用到了`redux-thunk`中间件  
actions 目录下保存各自的 action creater  
与同步不同的是：action 已变为函数形式

```
src
 |- actions // action creater
 |- components
 |- reducers
 |- store
```

```javascript
/**
 * action creater
 * @param params isObj，在组件中使用时传入的任意信息对象，例如异步请求接口地址
 * @param dispatch 在组件中使用时传入store.dispatch方法
 * @param getState 在组件中使用时传入store.getState方法
 * render函数会被触发两次
 */
const actionCreater = params => (dispatch, getState) => {
  // 交互派发Action，使视图重新渲染（可用于乐观请求操作）
  dispatch({
    type: 'GET_DATA',
    payload: '请求发送'
  });
  // HTTP success 派发 Action，使视图重新渲染
  setTimeout(() => {
    dispatch({
      type: 'GET_DATA_SUCCESS',
      payload: '请求成功'
    });
  }, 1000);
};
export default actionCreater;
```

与同步不同的是：store.dispatch 方法将作为参数传入 actionCreater 函数，在其内部触发使用  
使用：交互触发执行 actionCreater()(store.dispatch, store.getState) 方法

---

**HTTP**

引入`isomorphic-fetch`进行 Ajax 请求

使用：

```javascript
import fetch from 'isomorphic-fetch';
fetch('URL', {
  method: 'POST', // 请求方式
  // 请求头
  headers: {
    Accept: 'application/json',
    'Content-Type': 'application/x-www-form-urlencoded'
  },
  body: 'account=xxx&symbol=GOLD' // 请求体
})
  .then(res => res.json()) // JSON格式转JS对象
  .then(data => {
    // ...
  })
  .catch(() => {});
```

---

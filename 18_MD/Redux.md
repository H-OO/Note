## Redux

Redux 是一种架构，将 Flux 架构与函数式编程结合在一起；用于解决 React 不能开发大型复杂应用的问题。

------------------------------------------------------------------

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

------------------------------------------------------------------

**基本概念和API**

store 保存数据的地方，是一个对象
```
import { createStore } from 'redux';
const store = createStore(fn);
```

state 当前时刻的数据集合，一个state对应一个View
```
import { createStore } from 'redux';
const store = createStore(fn); // reducer函数
const state = store.getState();
```

action View发出的通知，表示state需要发生改变了
```
const action = {
  type: 'ADD_TODO', // 触发ADD_TODO的行为
  payload: 'Learn Redux' // 携带的信息
};
```

store.dispatch() 是View发出action的唯一方法
```
import { createStore } from 'redux';
const store = createStore(fn);
store.dispatch({
  type: 'ADD_TODO',
  payload: 'Learn Redux'
});
```

reducer函数 是createStore的参数，是一个纯函数，用于获取[全新]的state，使View更新  
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

store.subscribe() 订阅state的变化，触发View更新
```
let unsubscribe = store.subscribe(() =>
  console.log(store.getState())
  // ...
);
unsubscribe(); // 取消订阅
```

------------------------------------------------------------------


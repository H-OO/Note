## react@16.4.2

**安装与创建**
---
* 安装脚手架 `sudo npm i -g create-react-app`
* 创建react项目 `create-react-app appName`

**class**
---
ES6新增，类
```js
// es6
class obj {
  constructor(name) {
    this.name = name;
  }
  sayName() {
    console.log(this.name);
  }
}
// 等同于
// es5
function P (name) {
  this.name = name;
}
P.prototype.sayName = function () {
  console.log(this.name);
}
```

类通过关键字 extends 继承另一个类的所有属性和方法
```js
// A.js
class A {
  constructor() {
    console.log('__A constructor__');
  }
  sayHi() {
    console.log('Hi');
  }
}
export { A };

// B.js
import { A } from './A.js';
class B extends A {}

// use
const b = new B();
b.sayHi(); // Hi
```

super关键字  
作用：表示父类的构造函数，用来新建父类的this对象
```js
// F
class F {}
class C extends F {
  constructor() {
    super(); // 调用父类的constructor
  }
}
```
注意：  
子类必须在constructor方法中调用super方法，否则新建实例时会报错。  

原因：  
因为子类自己的this对象，必须先通过父类的构造函数完成塑造，  
得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。  
如果不调用super方法，子类就得不到this对象

ES5 的继承，实质是先创造子类的实例对象this，然后再将父类的方法添加到this上面。  
ES6 的继承，实质是先将父类实例对象的属性和方法，加到this上面（必须先调用super方法），再用子类的构造函数修改this。

子类没有定义constructor方法，这个方法会被默认添加。也就是说，不管有没有显式定义，任何一个子类都有constructor方法。

注意：只有在调用super之后才能使用this关键字
```js
class C extends F {
  constructor() {
    console.log(this); // ReferenceError
    super(); // 调用之后才能使用this
    console.log(this); // 正确
  }
}
```

ES5 中每一个对象都有__proto__属性，指向对应的构造函数的prototype属性  
ES6 中类同时拥有prototype属性和__proto__属性，存在两条继承链
```js
class F {}
class C extends F {}
console.log(C.__proto__ === F); // true
console.log(C.prototype.__proto__ === F.prototype); // true
```
* 子类的__proto__属性，表示构造函数的继承，总是指向父类
* 子类prototype属性的__proto__属性，表示方法的继承，总是指向父类的prototype属性

**import与export**
---
```js
// test.js
class One {}
class Two {}
export { One, Two };
```
```js
// App.jsx
import { One, Two } from 'test.js';
```

**创建与使用组件**
---
```js
// Test.component.jsx
import React, { Component } from 'react';
class Test extends Component {
  render() {
    return (
      <div>Test</div>
    )
  }
}
export default Test;

// App.jsx
import React, { Component } from 'react';
import Test from './Test.component.js';
class App extends Component {
  render() {
    return (
      <div>
        <h2>App</h2>
        <Test/>
      </div>
    )
  }
}
export default App;
```

**组件通信**
---
父组件向子组件传值，通过 this.props
```js
// 父组件 F.jsx
import C from './C.jsx';
class F extends Component {
  render() {
    <C msg='H_OO'/>
  }
}

// 子组件 C.jsx
class C extends Component {
  render() {
    return (
      <div>
        <h3>{this.props.msg}</h3>
      </div>
    )
  }
}
```

子组件改变值传递给父组件  
```js
// 父组件 F.jsx
import C from './C.jsx';
class F extends Component {
  constructor() {
    super();
    this.state.msg = 'H_OO';
    this.state = {
      msg: 'H_OO'
    };
    this.onMsgChange = this.onMsgChange.bind(this);
  }
  onMsgChange(newMsg) {
    this.setState({
      msg: newMsg
    })
  }
  render() {
    const msg = this.props.msg;
    return (
      <C msg={msg} onMsgChange={this.onMsgChange} />
    )
  }
}

// 子组件 C.jsx
class C extends Component {
  constructor() {
    super();
    this.clickHandler = this.clickHandler.bind(this);
  }
  clickHandler() {
    this.props.onMsgChange('__H_OO__');
  }
  render() {
    <div>
      <h3>{this.props.msg}</h3>
      <button onClick={this.clickHandler}>msgChange</button>
    </div>
  }
}
```
注意：this.props是只读的，任何修改都会报错

**ref**
---
ref属性用于获取React实例化创建出来的DOM或React元素  
* 方式1 组件获取自身DOM `<div ref={ele => this.ele = ele}></div>`
* 方式2 组件获取其他组件（React元素）
* 方式3 子组件向父组件暴露DOM
* 方式4 父组件向子组件暴露DOM（与方式3同理）
```js
/**
 * 方式2
 */
import Child from './Child';
class App extends Component {
  constructor() {
    super();
    this.myRef = React.createRef(); // {current: null}
  }
  render() {
    return (
      <div>
        <Child ref={this.myRef} />
      </div>
    )
  }
}
// 初始化后通过 this.myRef.current 获取

/**
 * 方式3
 * 在父组件中创建Ref对象传递给子组件
 * this.childRef = React.createRef();
 * <Child childRef={this.childRef}>
 * 
 * 子组件通过 this.props.childRef 进行接收
 * 子组件将在 this.props.childRef 赋值给需要暴露的DOM的ref属性
 * 
 * 组件初始化完毕后，父组件通过 this.childRef 获取DOM
 */
// Child.jsx
class Chlid extends Component {
  render() {
    return (
      <div>
        <div ref={this.props.childRef}>Child</div>
      </div>
    )
  }
}
// App.jsx
import Child from './Child';
class App extends Component {
  constructor() {
    super();
    this.childRef = React.createRef();
    this.getDOM = this.getDOM.bind(this);
  }
  getDOM() {
    console.log(this.childRef);
  }
  render() {
    return (
      <div>
        <button onClick={this.getDOM}>getD</button>
        <Child childRef={this.childRef} />
      </div>
    )
  }
}
```

**生命钩子函数**
---
* componentWillMount 组件挂载前调用
* componentDidMount 组件挂载完毕后调用
* componentWillUpdate 组件更新前调用
* componentDidUpdate 组件更新后调用
* componentWillUnmount 组件销毁前调用

## redux@4.0.0

`npm i -D redux`

```
目录结构
|- src
    |- actions
    |- components
    |- reducers
    |- store
```

redux相关
* store 状态仓库
* state 仓库某一时刻的快照
* action 信息对象
* reducer 计算过程

**store**
---
createStore接收 reducer 作为参数，生成一个新的 store
```js
// src/store/store.js
import { createStore } from 'redux';
const store = createStore(reducer)); // reducer为纯函数，用于计算出最新的state
```

**action**
---
用户触发 View(视图) 生成 action(信息对象)  
必须有 type 属性，用于判断类型  
注意：type 不能重名（属性值规范：组件名_类型）
```js
const action = {
  type: 'component_todosomething'
};
```

**store.dispatch 方法**
---
store.dispatch 方法是 View 发送 action 的唯一方法
```js
// 同步渲染
store.dispatch({
  type: 'todosomething',
  payload: '_todo_'
});

// 异步渲染
// src/action/actionCreater.js
const actionCreater = params => (dispatch, getState) => {
  dispatch(params); // 发送第一个 action
  // 异步获取数据再发送第二个 action
};
export default actionCreater;
```

**reducer**
---
reducer是一个纯函数，用于计算出最新的state
```js
// src/reducers/reducer.js
const reducer = (state = {}, action) => {
  /**
   * @param state 初始state为 {}
   * @param action 信息对象
   */
  switch (action.type) {
    case 'todosomething':
      return {
        payload: action.payload
      };
    default:
      return state;
  }
};
export default reducr;
```
reducer何时调用  
做法：将其作为 createStore 方法的参数获得一个新的store `createStore(reducer)`  
效果：这样当store.dispatch方法发送一个新的action，就自动调用reducer，计算出最新的state

**异步渲染**
---
获取服务器端的数据进行视图渲染  

异步渲染流程：  
View → Action → Dispatch → Store → State → View → Action → Dispatch → Store → State → View

总共发送两次 action

```js
// src/store/store.js
/**
 * @const createStore 创建 store，异步渲染需要接受两个参数
 * @const reducers 将多个reducer进行合并后的结果
 */
import { createStore } from 'redux';
import reducers from '../reducers';
const store = createStore(reducers);
export default store;
```
```js
// src/actions/TestActionCreater.js
/**
 * @param params isObj 接收到的信息对象（初始化对象由reducer函数提供）
 * @param dispatch isFunc 在组件调用该方法时传入 store.dispatch
 * @param getState isFunc 在组件调用该方法时传入 store.getState
 */
const TestActionCreater = params => (dispatch, getState) => {
  dispatch(params); // 发送第一个action
  // 异步获取数据再发送第二个 action
};
export default TestActionCreater;
```
```js
// src/reducers/TestReducer.js
/**
 * 注意：所有 reducer 需要合并后使用，而且所有的 type 属性必须是唯一的！
 * 在触发 store.dispatch 方法时，自动调用 reducer 方法，返回新的 state
 * @param state 当前时刻状态仓库的快照 （默认值作为初始化对象）
 * @param action 信息对象 dispatch 方法推送过来
 */
const TestReducer = (state = {
  payload: ''
}, action) => {
  switch (action.type) {
    // 初始化
    case 'xxx_init':
      return {
        payload: action.payload
      }
    // ...
    default:
      return state
  }
};
export default TestReducer;
```
```js
// src/reducers/index.js
/**
 * 合并所有 reducer，将 index 文件作为最终的输出文件
 * @param combineReducers 合并插件
 * @param Test 引入TestReducer
 */
import { combineReducers } from 'redux';
import Test from './TestReducer';
export default combineReducers({
  // 命名等同于创建命名空间 使用时：store.getState().Test isObj
  Test
});
```

**store.subscribe 方法**
---
`store.subscribe`方法用于订阅`store`，接收一个处理函数作为参数，返回取消订阅方法  
如果`store`中的状态发生改变，则调用该方法传入的处理函数  
↓↓
处理函数内部通过`store.getState().Test`获取最新的`state`  
再通过`this.setState`方法重新渲染回视图
```js
// src/components/Test/Test.jsx
import React, { Component } from 'react';
import store from '../../store/store';
import TestActionCreater from '../../actions/TestActionCreater';
class Test extends Component {
  constructor() {
    super();
    this.handler = this.handler.bind(this);
    this.state = {
      payload: store.getState().Test.payload
    };
  }
  handler() {
    // 仓库内的状态改变，将新状态同步到组件 state 对应的位置（从而触发视图更新）
    this.setState({
      payload: store.getState().Test.payload
    })
  }
  componentDidMount() {
    // 订阅
    const unsubscribe = store.subscribe(this.handler);
    // 将取消订阅的方法存放到 this.state 中
    this.setState({
      unsubscribe
    });
    // 初始化
    TestActionCreater({
      type: 'Test_init',
      payload: '_init_value_'
    })(store.dispatch, store.getState);
  }
  componentWillUnmount() {
    this.state.unsubscribe(this.handler); // 取消订阅
  }
  render() {
    return (
      <div>
        {this.state.payload}
      </div>
    )
  }
}
export default Test;
```
注意：  
1.先订阅后初始化  
2.从优化角度，组件销毁之前需要取消订阅

**HTTP**
---
`npm i -D isomorphic-fetch`
```js
// src/action/TestActionCreater.js
import fetch from 'isomorphic-fetch';
const TestActionCreater = params => (dispatch, getState) => {
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
      // 梳理数据
      // 调用 dispatch 方法发送信息对象
    })
    .catch(() => {});
};
export default TestActionCreater;
```

## react-router@4.3.1

`npm i -D react-router-dom`
```js
// App.jsx
import { BrowserRouter, Route } from 'react-router-dom';
<BrowserRouter>
  <Route />
</BrowserRouter>
```

**BrowserRouter**
---
BrowserRouter 组件使用 HTML5 提供的 history API  
相关方法
* popstate
* pushState
* replaceState
相关属性
* basename [=string] 根据项目目录存放路径进行设置，默认为 ''
* forceRefresh [=bool] 强制刷新
* getUserConfirmation [=func] 询问是否跳转到其他路径
* keyLength [=number] 设置路由的 location.key 的长度，默认为6

```js
/**
 * forceRefresh
 * 浏览器不支持 HTML5 提供的 history API 时启用
 */
<BrowserRouter
  forceRefresh={() => {
    return 'pushState' in window.history
  }}
>
</BrowserRouter>

/**
 * getUserConfirmation
 */
<BrowserRouter
  getUserConfirmation={(message, callback) => {
    const bool = window.confirm(message); // 弹出询问选项框 等待结果返回
    callback(bool);
  }}
>
</BrowserRouter>

/**
 * keyLength
 * key 指的是 location.key
 * 点击同一个路径时 key 会一直发生变化
 * 可以通过 key 的变化刷新页面
 */
```

**Route**
---
Route组件渲染内容可通过以下任意一个属性
* component
* children
* render
```js
import Nav from '/src/components/Nav/Nav'; // 导航组件
/**
 * Route
 */
<BrowserRouter>
  <Route />
</BrowserRouter>

/**
 * component [=React元素]
 * Route 组件通过 component 属性渲染内容
 */
<Route path='/' component={Nav} />

/**
 * children [=func]
 * Route 组件通过 children 属性渲染内容
 * 注意：无论 path 是否匹配都会渲染
 * path 不匹配 match 为 null
 */
<Route children={() => <div>Test</div>} />

/**
 * render [=func]
 * Route 组件通过 render 属性渲染内容
 */
<Route render={() => <div>Test</div>} />
```

Route组件 为内容组件传递的属性，通过 this.props 获取
* history
* location
* match
```js
/**
 * history isObj 
 * 相关属性 [只读]
 * action
 * 相关方法 [可调用]
 * block
 * createHref
 * go 根据参数，1表示前往下一页 -1表示前往上一页
 * goBack 前往上一页
 * goForward 前往下一页
 * listen
 * push
 * replace
 */

/**
 * location isObj [只读]
 * 相关属性
 * hash
 * key
 * pathname
 * search
 * state
 */

/**
 * match isObj [只读]
 * 相关属性
 * isExact
 * params
 * path
 * url
 */
```

**path**
---
```js
// 情景：查询商品详情时

import Goods from '/src/components/Goods/Goods'; // 商品详情组件

/**
 * 预先配置好 path 才能将商品id号传递给组件
 * :id  将 id 作为了组件 this.props.match.params 对象的属性
 */
<Route path='/goods/:id' component={Goods} />

/**
 * 用户点击某个链接查看商品，携带商品id号进行路由跳转
 * 注意：函数式需使用高阶路由组件，例如：BrowserRouter
 */
// 标签式
<Link to='/goods/1'>xx商品</Link>
// 函数式 (通过触发事件去执行)
this.props.history.push('/goods/1')

/**
 * this.props.match.params isObj
 * 商品详情组件内通过 this.props.match.params 获取商品id
 */
componentDidMount() {
  console.log(this.props.match.params.id); // 1
}
```

**匹配模式**
---
* exact [=bool] 完全精确
* strict [=bool] 严格
* sensitive [=bool] 路径是否区分大小写

**Redirect**
---
Redirect 路由重定向，新的路由地址会覆盖当前值
```js
// 登录页组件
<Route path='/login' component={Login} />

/**
 * 路由重定向到登录页面
 * to [string|object]
 */
// 字符串形式
<Redirect to='/login' />
// 对象形式（可携带自定义的state对象）
<Redirect to={{
  pathname: '/login',
  search: '?user=xxx',
  state: {}
}} />
```

**Switch**
---
Switch 组件只渲染与其路径匹配的第一个子<Route> 或 <Redirect>
```js
// 主页组件
<Route path='/home' component={Home} />

/**
 * from 属性
 */
// 将地址为 /unable (无法匹配的路径) 的路由重定向 到 /home
<Switch>
  <Redirect from='/unable' to='/home' />
</Switch>

/**
 * 实现功能：用户登录后跳转到首页，用户登出后跳转到登录页
 * Switch + Redirect + redux
 * loginState 为当前的登录状态，默认为false
 */
import { Switch, Redirect } from 'react-router-dom';
<Switch>
  {loginState && <Redirect from='/login' to='/home' />}
  {!loginState && <Redirect to='/login' />}
</Switch>
```

**NavLink**
---
NavLink 组件为点击跳转的路由按钮添加焦点样式
* to 前往的地址
* activeClassName 焦点类名
```js
import { NavLink } from 'react-router-dom';

<NavLink to='/about'>About</NavLink>;
// 编译 ↓↓
<a href='/about'>About</a>

/**
 * activeClassName
 * 元素被点击时启用 selected
 * 不设置默认为 active
 */
<NavLink to='/about' activeClassName='selected'>About</NavLink>

/**
 * activeStyle
 * 元素被点击时启用内嵌样式，权重最高
 */
const style = {
  color: 'red'
};
<NavLink to='/about' activeStyle={style}>About</NavLink>
```

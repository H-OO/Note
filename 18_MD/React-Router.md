# React-Router

react-router-dom @4.2.2

作用：使 UI 与 URL 保持一致

---

## API

★ 表示开发常用，也是重点要掌握的

* BrowserRouter ★
* HashRouter [兼容低版本浏览器]
* Link ★
* MemoryRouter [RN]
* NavLink ★
* Prompt ★
* Redirect ★
* Route ★
* Router [通用版路由]
* StaticRouter [用于服务器端渲染场景]
* Switch
* matchPath
* withRouter

---

**BrowserRouter**

使用 HTML5 提供的 history API (pushState, replaceState 和 popstate 事件) 来保持 UI 和 URL 的同步。

```js
<BrowserRouter
  basename={string}
  forceRefresh={bool}
  getUserConfirmation={func}
  keyLength={number}
>
  <App />
</BrowserRouter>
```

【 basename 】[string]

作用：所有位置的基准 URL，例如代码部署在服务器的子目录 web 下 basename: '/web'

```js
<BrowserRouter basename='/web'>
  <Link to='/index' />
</BrowserRouter>
↓↓
<a href='/web/index' />
```

【 forceRefresh 】[bool]

作用：强制页面刷新，只有在不支持 HTML5 history API 的浏览器中使用此功能

```js
const supportsHistory = 'pushState' in window.history;
<BrowserRouter forceRefresh={!supportsHistory} />;
```

【 getUserConfirmation 】[func]

作用：询问用户是跳转 URL，默认使用 window.confirm

```js
// 这是默认的确认函数
const getConfirmation = (message, callback) => {
  const allowTransition = window.confirm(message);
  // allowTransition is bool 确定为true 取消为false
  callback(allowTransition);
}
<BrowserRouter getUserConfirmation={getConfirmation} />
```

【 keyLength 】[number]

作用：设置路由的 location.key 的长度，默认是 6。key 的作用：点击同一个链接时，每次该路由下的 location.key 都会变化，可以通过 key 的变化刷新页面

```js
<BrowserRouter keyLength={12} />
```

---

**HashRouter**

使用 URL 的 hash 部分（即 window.location.hash）来保持 UI 和 URL 的同步。仅用于支持低版本浏览器。

---

**Link**

为应用提供声明式的、可访问的导航链接，最后解析为 a 标签

```js
import { Link } from 'react-router-dom';
<Link to='/about'>About</Link>
// 解析后↓↓
<a href='/about'>About</a>
```

【 to 】[string|object]

字符串形式的链接地址

通过 pathname、search 和 hash 属性创建

```js
<link to='/courses?sort=name'>
```

对象形式的链接地址

通过 pathname、search、hash 和 state 属性创建

```js
<Link
  to={{
    pathname: '/courses', // 要链接到的路径
    search: '?sort=name', // 查询参数
    hash: '#the-hash', // URL 中的 hash，例如 #the-hash
    state: {} // 存储到 location 中的额外状态数据
  }}
/>
```

【 replace 】[bool]

跳转的链接是否覆盖 History 中当前的 URL  
true：表示新的 URL 将会覆盖 History 中的当前值，而不是向其中添加一个新的 URL  
默认为 false，跳转链接就往 History 中添加一个新的 URL

```js
<Link to='/courses' replace />
```

【 innerRef 】[func]

获取最终编译出来的 dom 元素，也就是一个 a 标签

```js
const refCallback = node => {
  // node 指向最终挂载的 DOM 元素，在卸载时为 null
}
<Link to='/' innerRef={refCallback} />
```

【 others 】

可以传递一些其他属性，例如 title、id 或 className 等。

---

**NavLink**

匹配路由跳转后为点击跳转的 a 标签添加焦点样式

```js
import { NavLink } from 'react-router-dom';
<NavLink to='/about'>About</NavLink>;
```

【 activeClassName 】[string]

元素处于激活状态时应用的样式类名，默认为 active

```js
<NavLink to='/faq' activeClassName='selected'>FAQs</NavLink>
// 编译↓↓
<a href='/faq' class='selected'>FAQs</a>
```

【 activeStyle 】[object]

当元素处于激活状态时应用的内嵌样式，权重最高

```js
const activeStyle = {
  fontWeight: 'bold',
  color: 'red'
};
<NavLink to='/faq' activeStyle={activeStyle}>
  FAQs
</NavLink>;
```

【 exact 】[bool]

只有在路由地址完全匹配时才应用激活样式类名/内嵌样式

```js
<NavLink exact to='/profile'>
  Profile
</NavLink>
```

【 strict 】[bool]

非完全匹配激活

```
http://www.x.com/home/xx
<NavLink to='/home'></NavLink>

以上能够激活样式类名/内嵌样式
```

【 isActive 】[func]

激活状态的回调函数

```js
const isActiveFunc = (macth, location) => {
  // match isObj 成员有path、url、isExact、params
  // location isObj 成员有pathname、hash、search、key、state
};
```

【 location 】[object]

传入会替换原有的 location 对象，相对于拿到一个自定义的 location 对象

```js
const location = {
  hash: '',
  key: '',
  pathname: '',
  search: '',
  state: ''
}
```
---

**Prompt**

当路由将要发生跳转之前给予用户一些确认信息。（例如在表单只填写了一半的场景）

```js
import { Prompt } from 'react-router-dom';

<Prompt
  when={formIsHalfFilledOut}
  message='你确定要离开当前页面吗？'
/>
```

【 message 】[func|string]

当用户试图离开某个位置时弹出的提示信息。

最终结果可return [string|bool]

string会弹出提示框给用户选择

true允许路由跳转，无提示框

false不允许路由跳转，无提示框

【 when 】[bool]

决定是否渲染该组件

```js
// func
<Prompt when={true} message={
  location => {
    const msg = '你确定要离开当前页面吗？';
    // 当msg为bool，无提示框，true|false会控制是否跳转路由
    // 根据需求决定
    return msg;
  }
} />
// string
<Prompt when={true} message='你确定要离开当前页面吗？' />
```
---

**Redirect**

路由重定向，新的 URL 将会覆盖 History 中的当前值

```js
import {Route, Redirect} from 'react-router-dom';
import Login from './components/Login/Login/js'; // Login组件
// 路由重定向至登录页
<Redirect to='/login' />
<Route path='/login' component={Login}>
```

【 to 】[string|object]

```js
// 字符串形式
<Redirect to='/login' />
// 对象形式 (可携带自定义状态)
<Redirect to={{
  pathname: '/login',
  search: '?user=xxx',
  state: {}
}} />
```

【 push 】[bool]

重定向是否将新的位置推入历史记录，而不是替换当前值

【 from 】[string]

只能再 Switch 组件下配合使用

```js
// 将前往 /personal 的路由重定向 到 /login
<Switch>
  <Redirect from='/personal' to='/login' />
</Switch>
```

【 exact 】[bool]

完全匹配

【 strict 】[bool]

严格匹配

与Switch组件配合，能实现用户登录登出操作的路由重定向跳转

```js
// 根据登录状态进行判断 假设 loginState 能获取当前登录状态 默认为false
import { Switch, Redirect } from 'react-router-dom';
// 当 loginState 状态改变，并且回馈给了组件，现在为true
// 实现功能：用户登录后跳转首页，用户登出后跳转登录页
<Switch>
  {loginState && <Redirect from='/login' to='/home' />}
  {!loginState && <Redirect from='/personal' to='/login' />}
</Switch>
```

Switch组件后面将会介绍，不要方

---

**Route**

```js
import { BrowserRouter as Router, Route } from 'react-router-dom';
<Router>
  <div>
    <Route exact path='/' component={Home} />
    <Route path='/news' component={News} />
  </div>
</Router>
```

使用<Route>渲染一些内容有以下三种方式：  
* component
* render
* children

三种渲染方式都将提供相同的三个路由属性  
* match
* location
* history

【 component 】[React元素]

component属性接收Home组件，路由为'/'就渲染Home组件

```js
<Route path='/' component={Home} />
```

【 render 】[func]

render属性接收一个函数，由该函数返回结果进行渲染
```js
// 内联渲染
<Route path='/home' render={() => <div>Home</div>} />
```

【 children 】[func]

无论 path 是否匹配位置都会渲染

path不匹配match为null

此处待续!!!

【 path 】[string]

```js
<link to='/goods/1' />Goods</Link>
<Route path='/goods/:id' component={Goods} />
```
:id 将id作为 match.params的key  
点击跳转后传入值为1  
Goods组件中 this.props.match.params 为 {id: 1}

【 exact 】[bool]

完全匹配

【 strict 】[bool]

严格匹配

【 sensitive 】[bool]

匹配路径是否区分大小写

---

**Router**

低阶通用版 Router 组件

其他高阶 Router 组件
* BrowserRouter
* HashRouter
* MemoryRouter
* NativeRouter
* StaticRouter

---

**Switch**

渲染与路径匹配的第一个子<Route> 或 <Redirect>

<Switch>只会渲染一个路由

---

**withRouter**

访问 history 对象

---

**函数中进行路由跳转**

需要使用高阶组件 BrowserRouter  
访问 history，使用push方法，参数为路由地址  
```js
this.props.history.push('/')
```
---
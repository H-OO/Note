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
* Route
* Router
* StaticRouter
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
<Link to="/courses" replace />
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
<NavLink to="/about">About</NavLink>;
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
<NavLink to="/faq" activeStyle={activeStyle}>
  FAQs
</NavLink>;
```

【 exact 】[bool]

只有在路由地址完全匹配时才应用激活样式类名/内嵌样式

```js
<NavLink exact to="/profile">
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
  message="你确定要离开当前页面吗？"
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
    const msg = "你确定要离开当前页面吗？";
    // 当msg为bool，无提示框，true|false会控制是否跳转路由
    // 根据需求决定
    return msg;
  }
} />
// string
<Prompt when={true} message="你确定要离开当前页面吗？" />
```
---

**Redirect**

路由重定向，新的 URL 将会覆盖 History 中的当前值


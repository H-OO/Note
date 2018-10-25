## ts

## **基本类型**

- boolean
- number
- string
- undefined
- null
- 数组 number[] / Array<number|string>
- 元组 [number, string]
- 枚举 enum {First = 0, Second, Third}
- any 模糊类型
- void 没有任何类型，只能是 undefined 和 null
- never 函数存在无法到达的终点，例如抛出错误，死循环等
- 类型断言 <> / as，jsx 中只能使用 as
- 对象检查 <接口>{}

注：默认情况下，undefined 和 null 是所有类型的子类型

```ts
// 数组解构
let [first, second]: [number, string] = [123, 'yy'];
console.log(first, second);
// 对象解构
let { a, b }: { a: number; b: string } = { a: 123, b: 'yy' };
// 对象解构-属性重命名
let { a: first, b: second }: { a: number; b: string } = { a: 123, b: 'yy' };
console.log(first, second); // 123 'yy'
// 对象解构-缺少值时
let { a: first, b: second = 'oo' }: { a: number; b?: string } = { a: 123 };
console.log(first, second); // 123 'oo'
// 函数参数-数组解构
function foo([first, second]: [number, string]): void {
  console.log(first, second);
}
foo([123, 'yy']);
// 函数参数-对象解构
function foo({ first, second }: { first: number; second: string }): void {
  console.log(first, second);
}
foo({ a: 123, b: 'yy' });
// 函数参数-返回值
type C = { a: number; b: string }; // 方式2 interface C {a: number, b: string};
function foo(param: C): object {
  return param;
}
foo({ a: 123, b: 'yy' });
// 函数参数-接口
interface Foo {
  (first: string, second: number): void;
}
const foo: Foo = function(first, second) {
  console.log(first, second); // 'yy' 123
};
foo('yy', 123);
// 数组-索引类型
interface Arr {
  [index: number]: string;
}
const arr: Arr = ['a', 'b', 'c'];
const a = arr[0];
console.log(a); // 'a'
// 对象-索引类型
interface Obj {
  [index: string]: number;
}
const obj: Obj = {
  first: 0,
  second: 1
};
console.log(obj['first']);
// 类-类型（公共部分）
interface Person {
  name: string;
  sayAge(age: number): number;
}
class P implements Person {
  name: string;
  constructor(name: string) {
    // 私有部分接口不能对其进行检查
    this.name = name;
  }
  sayAge(age: number) {
    return age;
  }
}
const p = new P('yy');
console.log(p.name, p.sayAge(18)); // 'yy' 18
// 类-类型（私有部分处理）
interface PIF {
  name: string;
}
interface PCtor {
  new (name: string, age: number): PIF;
}
class P implements PIF {
  name: string;
  age: number;
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
}
function createP(ctor: PCtor, name: string, age: number): PIF {
  return new ctor(name, age);
}
const p = createP(P, 'yy', 18);
console.log(p); // {name: "yy", age: 18}
// 类型断言
let str: any = 'abcde';
let strLength: number = <string>str.length; // 方式1，不能用于.tsx
let strLength: number = (str as string).length; // 方式2，可用于.tsx
```

## **接口**

- :
- interface A { first: string, second?: number }
- type A = { first: string, second?: number }
- 对象只读属性，属性前面加 readonly
- 只读数组 ReadonlyArray<any>
- 额外属性 [propName: string]: any
- 索引类型 数组[index: number]: any，对象[index: string]: any
- 类类型 implements (接口只描述类的公共部分)

## **类**

属性成员

- public 公共，外部可访问(默认)
- private 私有，外部不可访问
- protected 子类内部可以访问父类中使用该标记的属性，子类外部不可访问
- readonly 只读属性，只能在构造函数中初始化
- static 静态属性，属性值不会再发生变化，只能在内部访问
- abstract 抽象类，抽象类不能创建实例，抽象类中用`abstract`标记的属性需要在派生类中实现

- extends 继承
- implements 接口检测

注意：派生类中定义的方法需在它的父类进行查找，也就是保存在原型对象中

```ts
// 公共&私有
interface PIF {
  name: string;
  age: number;
  sayPhone(): void;
}
class P implements PIF {
  public name: string;
  public age: number;
  private phone: string;
  constructor(name: string, age: number, phone: string) {
    this.name = name;
    this.age = age;
    this.phone = phone;
  }
  sayPhone(): void {
    console.log(this.phone);
  }
}
const p = new P('yy', 18, '150');
p.sayPhone(); // '150'
p.phone; // err 外部无法访问私有属性

// 存储器属性
class P {
  private _name: string;
  get name(): string {
    return this._name;
  }
  set name(newName) {
    this._name = newName;
  }
}
const p = new P();
p.name = 'yy';
console.log(p.name);

// 抽象类
abstract class F {
  constructor(public name: string) {}
  abstract printName(): void; // 必须在派生类中实现
}
// 派生类
class C extends F {
  constructor(name: string) {
    super(name); // 派生类构造函数必须调用`super`方法
  }
  printName(): void {
    console.log(this.name);
  }
}
// 抽象类不能创建实例
// 抽象类中用`abstract`标记的属性需要在派生类中实现
const c0: C = new C('yy');
console.log(c0.name); // 'yy'
c0.printName(); // 'yy'
const c1: C = new C('oo');
console.log(c1.name); // 'oo'
c1.printName(); // 'oo'

// 类作为接口
class P1 {
  name: string;
  age: number;
}
interface P2 extends P1 {
  add: string;
}
const p: P2 = { name: 'yy', age: 18, add: 'SZ' };
console.log(p); // {name: "yy", age: 18, add: "SZ"}
```

## **函数**

```ts
// 重载
function handler(param: object): number;
function handler(param: number): string;
function handler(param: any): any {
  if (typeof param === 'object') {
    return 123
  } else if (typeof param === 'number') {
    return 'abc'
  }
}
const res0 = handler({});
console.log(res0); // 123
const res1 = handler(0);
console.log(res1); // 'abc'
```

## **命名空间**

```ts
// n.ts
/**
 * 命名空间
 * 提供逻辑分组，避免命名冲突问题
 */
export namespace N {
  export class A {}
  export class B {}
}

// App.ts
import { N } from 'n.ts';
console.log(N); // {A: ƒ, B: ƒ}
```


## **.d.ts文件**

作用: 描述JS模块内所有导出接口的类型信息

原因：在ts文件中无法从js文件中摘取类型信息，这会导致编译报错

解决：通过`.d.ts`文件写入声明信息，通过编译

```ts
// 解决1：创建`try.d.ts`文件
declare let test: () => void
// 解决2：在ts文件中使用 `三斜线指令`引用`try.d.ts`
/// <reference path='./try.d.ts'>
```

## **使用第三方js库**



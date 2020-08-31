# ES6 快速入门

## 任务 1：let & const

let 可以解决作用域污染问题和局部作用域的问题
在同一个作用域变量只可以声明一次同一个变量 // Identifier 'a' has already been declared
不存在预解释问题（变量提升），即存在暂存死区
暂存死区，如果作用域内有这样一个变量，那么这个作用域内就会绑定这个变量，不会继续向上查找了
通过 const 声明的变量不能被修改

## 任务 2： 解构赋值

声明和赋值都放到一起了
解构表示等号左边和右边结构类似

## 任务 3：模板字符串

可以支持换行 可以支持取值（不用转义）

扩展问：如何自己实现一个类模板字符串的功能

```js
let name = 'zfpx';
let age = 9;
let str = 'hello~${name}今年${age}岁了';

str = str.replace(/\$\{([^}]*)\}/g, function () {
  return eval(arguments[1]);
});
console.log(str);
```

带标签的模板字符串 自定义模板字符串的实现

```js
let name = 'zfpx';
let age = 9;
function jw() {
  // 第一个参数是字符串的数组 第二个参数是第一个变量...
  let strings = arguments[0];
  let values = [].slice.call(arguments, 1);

  let str = '';
  for (let i = 0; i < values.length; i++) {
    str += `${strings[i]}${values[i]}`;
  }
  str += strings[strings.length - 1];
  return str;
}
let str = jw`hello~${name}今年${age}岁了`;
```

```js
let url = 'http://www.zhufengpeixun.cn/logo.png';
// includes 是否包含
console.log(url.includes('zhufengpeixun'));
// startsWith 以 xxx 开头
console.log(url.startsWith('http://'));
// endsWith 以 xxx 结尾
console.log(url.endsWith('.png'));
// padStart padEnd 补全
// 进制转换

setInterval(function () {
  let date = new Date();
  let hour = date.getHours();
  let minutes = date.getMinutes();
  let seconds = date.getSeconds();
  let str = `${hour.toString().padStart(2, 0)}:`;
  str += `${minutes.toString().padStart(2, 0)}:`;
  str += `${seconds.toString().padStart(2, 0)}`;
  console.log(str);
}, 1000);
```

## 任务 4：箭头函数

## 任务 5：收集 & 展开运算符

扩展：实现深拷贝

```js
function deepClone(obj) {
  if (obj === null) return null;
  if (typeof obj !== 'object') return obj;

  if (obj instanceof Date) return new Date(obj);
  if (obj instanceof RegExp) return new RegExp(obj);

  // if (Object.prototype.toString.call(obj) === '[object Array]') {
  // let arr = [/]
  let o = new obj.constructor(); // 保留类的继承关系
  for (let key in obj) {
    o[key] = typeof obj[key] === 'object' ? deepClone(obj[key]) : obj[key];
  }
  return o;
  // }
}
```

## 任务 6：数组的方法

map(some, every, filter, forEach) 是 es5 的
find findIndex es6  
reduce 收敛 叠加，返回的结果是叠加后的结果，函数的返回结果会作为下一次循环的 prev
for of()

Array.from
of()

```js
[1, 2, 3, 4, 5].reduce((prev, next, currIndex, ary) => {
  return prev + next;
}, 0);

// 模拟实现 reducer
Array.prototype.myReduce = function (fn, prev) {
  for (let i = 0; i < this.length; i++) {
    if (typeof prev === 'undefined') {
      prev = fn(this[i], this[i + 1], i + 1, this);
      ++i;
    } else {
      prev = fn(prev, this[i], i, this);
    }
  }
  return prev;
};

// 模拟实现 forEach
Array.prototype.forEach = function (fn) {
  for (let i = 0; i < this.length; i++) {
    fn(this[i], i);
  }
};

// 模拟实现 map
Array.prototype.map = function (fn) {
  let arr = [];
  for (let i = 0; i < this.length; i++) {
    arr.push(fn(this[i], i));
  }
  return arr;
};

// 模拟实现 filter
Array.prototype.filter = function (fn) {
  let arr = [];
  for (let i = 0; i < this.length; i++) {
    const result = fn(this[i], i);
    if (result) arr.push(result);
  }
  return arr;
};
```

## 任务 7：对象

Object.assign(obj1, obj2);

Object.setPrototypeOf(obj1, obj2);

`__proto__` 链

## 任务 8：继承 fbr

在 es5 中没有类的概念的

es6 中新加了 class

类的继承 三种属性 公有属性 私有属性 静态方法（静态属性）

```js
function Parent() {
  this.name = 'parent';
}
Parent.prototype.eat = function () {
  console.log('eat');
};

Parent.prototype.constructor === Parent;

let parent = new Parent();

parent.__proto__ === Parent.prototype;

// 1. 只继承私有属性

function Child1() {
  this.age = 9;
  Parent.call(this); // 继承私有属性
}

// 2. 只继承公有属性

function Child2() {
  this.age = 9;
}
Child2.prototype.smoking = function () {
  console.log('smoking');
};
Child2.prototype.__proto__ = Parent.prototype; // 或
Object.setPrototypeOf(Child.prototype, Parent.prototype); // 或
Child.prototype = Object.create(Parent.prototype, { constructor: { value: Child } });

// 自己实现 Object.create
function create(parentPrototype) {
  function Fn() {}
  Fn.prototype = parentPrototype;
  let fn = new Fn();
  for (let key in props) {
    Object.defineProperty(fn, key, {
      ...props[key],
      enumerable: true,
    });
  }
  return fn;
}

// 再扩展 defineProperty

Object.defineProperty(a, 'name', {
  enumerable: true, // 表示这个属性是否可以被枚举出来
  configurable: true, // 表示这个属性是否可被删除
  // writable: true, // 表示这个属性是否可被修改
  get() {
    // value: 1, 可以替换成 set 和 get，并且这个时候不能设置 writable 了
    console.log('get');
    return 1;
  },
  set(val) {
    console.log('设置值');
  },
});
// 继承公有属性和私有属性

Child.prototype = new Parent(); // 不会使用这种方式
```

## 任务 9：类的编译 && 任务 10：类的继承

1. 类只能 new
   class constructor Child cannot be invoked without 'new'

2. 类可以继承公有私有和静态方法
3. 父类的构造函数中返回了一个引用类型会把这个引用类型作为子类的 this

```js
// 手写 class 的 babel
// 类的调用检测 检测实例是不是 new 出来的
function _classCallCheck(instance, constructor) {
  if (!(instance instanceof constructor)) {
    throw new Error(`class constructor Child cannot be invoked without 'new'`);
  }
}

function definePropertys(target, arr) {
  for (let i = 0; i < arr.length; i++) {
    Object.defineProperty(target, arr[i].key, {
      ...arr[i],
      configurable: true,
      enumerable: true,
      writeable: true,
    });
  }
}

// construtor 是构造函数
// protoPropertys 是原型方法的描述
// staticPropertys 是静态方法的描述
function _createClass(constructor, protoPropertys, staticPropertys) {
  if (protoPropertys.length > 0) {
    definePropertys(constructor.prototype, protoPropertys);
  }
  if (staticPropertys.length > 0) {
    definePropertys(constructor, 是静态方法的描述);
  }
}
let Parent = (function () {
  function P() {
    _classCallCheck(this, P);
  }
  _createClass(
    P,
    [
      {
        key: 'eat',
        value: function () {
          console.log('吃');
        },
      },
    ],
    [
      {
        key: 'b',
        value: function () {
          return 2;
        },
      },
    ]
  );
  return P;
})();

// 子类继承父类
function _inherits(subClass, superClass) {
  // 继承公有属性
  subClass.prototype = Object.create(superClass.prototype, { constructor: { value: subClass } });

  // 继承静态方法
  Object.setPrototypeOf(subClass, superClass);
}

let Child = (function (Parent) {
  // 先实现继承父类的公有属性和静态方法
  _inherits(C, Parent);
  function C() {
    _classCallCheck(this, C);
    let obj = Parent.call(this);
    let that = this;
    if (typeof obj === 'object') {
      that = obj;
    }
    that.age = 9;
    return that;
  }

  return C;
})(Parent);

Parent();
```

## 任务 11: promise

Promise 是一种异步流程的控制手段
promise 关键字 resolve 成功 reject 失败 pending 等待态

Promise 只有一个参数 叫 excutor 执行器，默认 new 时就会调用

默认 promise 中的 executor 是同步执行的

# Generator

## 基本用法

Generator 函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同。执行 Generator 函数会返回一个遍历器对象，可以依次遍历 Generator 函数内部的每一个状态。

Generator 函数是一个普通函数，有两个特征：

1. function 关键字与函数名之间有一个星号；
2. 函数体内使用 yield 表达式，定义不同的内部状态。

Generator 函数的调用方法与普通函数一样，也是在函数名后面加上一对圆括号。调用 Generator 函数后，该函数并不执行，返回的是一个指向函数内部状态的指针对象，也就是遍历器对象（Iterator Object）。Generator 函数是分段执行的，yield 表达式是暂停执行的标记，而 next 方法可以恢复执行。

```js
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}

var hw = helloWorldGenerator();

hw.next();
// { value: 'hello', done: false }
hw.next();
// { value: 'world', done: false }
hw.next();
// { value: 'ending', done: true }
hw.next();
// { value: undefined, done: true }
```

每次调用遍历器对象的 next 方法，会返回一个有着 value 和 done 两个属性的对象。value 属性表示当前内部状态的值，是 yield 表达式后面那个表达式的值；done 属性是一个布尔值，表示是否遍历结束。

## yield 表达式

- yield 表达式后面的表达式，只有当调用 next 方法，内部指针指向该语句时才会执行；
- yield 表达式只能用在 Generator 函数里面，用在其他地方都会报错；
- yield 表达式如果用在另一个表达式中，必须放在圆括号里面；
- yield 表达式用作函数参数或放在赋值表达式的右边，可以不加括号。
- yield 表达式没有返回值，或者说返回 undefined。next 方法可以带一个参数，该参数会被当作上一个 yield 表达式的返回值。

**注：由于 next 方法的参数表示上一个 yield 表达式的返回值，所以在第一次使用 next 方法时，传递的参数是无效的。V8 引擎直接忽略第一次使用 next 方法时的参数。**

```js
// yield只能用在Generator函数内
(function (){
  yield 1;
})()
// SyntaxError: Unexpected number

// yield表达式在另一个表达式中，必须放在圆括号里面
function* demo() {
  console.log('Hello' + yield); // SyntaxError
  console.log('Hello' + yield 123); // SyntaxError

  console.log('Hello' + (yield)); // OK
  console.log('Hello' + (yield 123)); // OK
}

// yield表达式作函数参数、放在赋值表达式的右边，可以不加括号
function* demo() {
  foo(yield 'a', yield 'b'); // OK
  let input = yield; // OK
}

// next带的参数当作上一个yield表达式的返回值
function* f() {
  for(var i = 0; true; i++) {
    var reset = yield i;
    if(reset) { i = -1; }
  }
}

var g = f();

g.next() // { value: 0, done: false }
g.next() // { value: 1, done: false }
g.next(true) // { value: 0, done: false }
```

## Generator.prototype.throw\(\)

Generator 函数都有一个 throw 方法，可以在函数体外抛出错误，然后在函数体内捕获。throw 方法可以接收一个参数，该参数被 catch 语句接收。throw 方法抛出的错误要被内部捕获，前提是必须至少执行依次 next 方法（因为首次执行 next 方法，等同于启动执行 Generator 函数的内部代码），throw 方法被捕获之后，会附带执行一次 next 方法。

```js
var g = function*() {
  try {
    yield;
  } catch (e) {
    console.log('内部捕获', e);
  }
};

var i = g();
i.next();

try {
  i.throw('a');
  i.throw('b');
} catch (e) {
  console.log('外部捕获', e);
}
// 内部捕获 a
// 外部捕获 b
```

Generator 函数体外抛出的错误，可以在函数体内捕获；反过来，Generator 函数体内抛出的错误，也可以被函数体外的 catch 捕获。

## Generator.prototype.return\(\)

Generator 函数有一个 return 方法，可以返回给定的值，并且终结遍历 Generator 函数。如果不提供参数，则返回值的 value 属性为 undefined。如果 Generator 函数内部有 try...finally 代码块，那么 return 方法会推迟到 finally 代码块执行完再执行。

```js
function* numbers() {
  yield 1;
  try {
    yield 2;
    yield 3;
  } finally {
    yield 4;
    yield 5;
  }
  yield 6;
}
var g = numbers();
g.next(); // { value: 1, done: false }
g.next(); // { value: 2, done: false }
g.return(7); // { value: 4, done: false }
g.next(); // { value: 5, done: false }
g.next(); // { value: 7, done: true }
```

## yield\* 表达式

如果在 Generator 函数内部调用另一个 Generator 函数，需要使用 yield\* 表达式。yield\* 表达式 Generator 函数（没有 return 语句时），等同于在 Generator 函数内部，部署一个 for ... of 循环。

```js
function* bar() {
  yield 'x';
  yield* foo();
  yield 'y';
}

// 等同于
function* bar() {
  yield 'x';
  yield 'a';
  yield 'b';
  yield 'y';
}
```

实际上，任何数据结构只要有 Iterator 接口，就可以被 yield\* 遍历。

yield\* 命令可以很方便地取出嵌套数组的所有成员

```js
function* iterTree(tree) {
  if (Array.isArray(tree)) {
    for (let i = 0; i < tree.length; i++) {
      yield* iterTree(tree[i]);
    }
  } else {
    yield tree;
  }
}

const tree = ['a', ['b', 'c'], ['d', 'e']];

for (let x of iterTree(tree)) {
  console.log(x);
}
// a
// b
// c
// d
// e
```

## Generator 函数的 this

Generator 函数总返回一个遍历器，该遍历器继承了 Generator 函数的 prototype 对象上的方法。

```js
function* g() {}

g.prototype.hello = function() {
  return 'hi!';
};

let obj = g();

obj instanceof g; // true
obj.hello(); // 'hi!'
```

但，若把 g 当作普通的构造函数并不会生效，因为 g 返回的是遍历器对象，而不是 this 对象。Generator 函数跟 new 命令一起用会报错。

```js
function* g() {
  this.a = 11;
}

let obj = g();
obj.next();
// obj对象拿不到属性a
obj.a; // undefined

function* F() {
  yield (this.x = 2);
  yield (this.y = 3);
}

new F();
// TypeError: F is not a constructor
```

下面是一个变通方法。首先生成一个空对象，使用 call 方法绑定 Generator 函数内部的 this

```js
function* F() {
  this.a = 1;
  yield (this.b = 2);
  yield (this.c = 3);
}
var obj = {};
var f = F.call(obj);

// 遍历器对象是f
f.next(); // Object {value: 2, done: false}
f.next(); // Object {value: 3, done: false}
f.next(); // Object {value: undefined, done: true}

// 对象的实例是obj
obj.a; // 1
obj.b; // 2
obj.c; // 3
```

将遍历器对象 f 与对象实例 obj 统一起来。

```js
function* gen() {
  this.a = 1;
  yield (this.b = 2);
  yield (this.c = 3);
}

function F() {
  return gen.call(gen.prototype);
}

var f = new F();

f.next(); // Object {value: 2, done: false}
f.next(); // Object {value: 3, done: false}
f.next(); // Object {value: undefined, done: true}

f.a; // 1
f.b; // 2
f.c; // 3
```

参考链接：[http://es6.ruanyifeng.com/\#docs/generator](http://es6.ruanyifeng.com/#docs/generator)

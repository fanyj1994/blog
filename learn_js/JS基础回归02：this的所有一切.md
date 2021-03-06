### 认识 this

this 是 JS 中的一个关键字，有了函数就有了 this，对于 this，最直观的认识莫过于以下两种：

1. 它指向函数自身
2. 它指向函数的词法作用域

这两种认识都是错误的。this 到底指向哪里，取决于**函数在哪里被调用**。

怎么讲？

函数被创建的时候，this 是不存在的，this 在函数被调用时动态绑定。它代表了当前函数的执行上下文。

### this 到底指向哪里

this 指向哪里，有一个最简单粗暴的判定方法：

**它指向最后调用函数的对象。**

怎么确定调用函数的对象？

找到函数被调用的位置，调用的位置就是调用栈的第二位。

我们举例来看：

``` JS
const name = 'Alice'
function sayName() {
  const name = 'Bob'
  return this.name
}

const obj = {
  name: 'Tom',
  sayName: function() {
    return this.name
  }
}

sayName()
obj.sayName()
```

我们首先看直接调用sayName的时候，它的调用位置是全局环境，这个调用等价于 `window.sayName()`，所以，this 指向 window，这时候的 `this.name` 就是 `window.name`，所以，返回了全局环境中的 name 为 Alice。

在第二种调用情况中，我们可以清晰地看到最后调用函数的对象是 `obj`，所以 `this` 指的是 `obj`，`this.name` 指的是 `obj.name`，也就是 Tom。

下面，我们来具体细看不同情况下的 this 指向。

#### 1. 默认绑定

默认绑定，就是上面例子中的第一种调用情况，直接的函数调用，前面没有任何 `.` 操作。此时，this 指向的是全局对象，在浏览器中，正是 window。

要注意的是，在严格模式下，this 无法绑定全局对象，它会绑定为 undefined.

#### 2. 隐式绑定

隐式绑定是上面例子中的第二种情况。也就是说，直观观察函数调用前面有没有其他的调用者，在上面，obj 对象调用了 sayName，所以，sayName 执行的时候，this 被绑定到 obj，所以，`this.name` 等价于 `obj.name`。

在这种绑定情况中，只有调用链的最后一层才会被绑定。举个例子：

``` JS
const sayName = function () {
  return this.name
}
const obj1 = {
  name: 'Tom',
  sayName: sayName
}

const obj2 = {
  name: 'Alice',
  obj1: obj1
}

console.log(obj2.obj1.sayName()) // Tom
```

在上面的调用链中，obj1 是调用的最后一环，所以，this 绑定到 obj1 身上，返回值为 Tom.

#### 3. 显式绑定

在 JS 中，可以在某个对象上强制调用某个函数，将 this 强制绑定到这个对象，这个工作由 `call()` 和 `apply()` 两个方法实现。

这两个方法可以完成的工作类似。都接受第一个参数，作为要绑定的对象，将 this 绑定给它，后面可以传入参数。

两者传参的方式稍有不同，`call` 可以一个一个地传入参数，`apply` 可以传入一个参数数组。

看个例子：

``` JS
const name = 'Alice'
const obj = {
  name: 'Tom'
}

function sayName() {
  return this.name
}

sayName.call(obj) // Tom
```

在上面的例子中，this 通过 call 方法绑定给 obj，达到了显式地改变 this 指向的目的。

JS 中还提供一个显式绑定的模式： bind 绑定，bind 函数会返回一个新的函数，它的工作是，把 this 绑定到传入的参数，并调用原始函数。

#### 4. new 绑定

我们在[JS基础回归01：new操作符，原型和原型链](JS基础回归01：new操作符，原型和原型链.md) 中；了解过 new 操作符的工作原理，通过构造函数新建一个实例时，this 会被绑定到这个新的实例，所以，new 操作符是改变 this 的第四种方式。

``` JS
function Person(name) {
  this.name = name
}

const p1 = new Person('Tom')
console.log(p1.name) // Tom
```

#### 5. 箭头函数中的 this

ES6 中引入了箭头函数。箭头函数中的 this 指向，与前面的四种情况完全不同。事实上，箭头函数设计的主要目的就是为了解决前面这些 this 指向的纷乱情况。

箭头函数中的 this，并非是被调用时确定，**而是由其所在的词法作用域决定**，也就是说，箭头函数的 this 是**不能改变**的，其表现与普通变量的查找过程一致，沿着作用域链进行查找。

它沿着作用域链向上查找，知道找到第一个拥有 this 的函数，并将自己的 this 指向调用该函数的对象。举例来看：

``` JS
const name = 'Alice'
const obj = {
  name: 'Bob'
}

function test() {
  // 箭头函数中的 this 取决于 test的 this
  setTimeout(() => console.log(this.name), 100)
}

test() // Alice
test.call(obj) // Bob
```

直接调用 test 函数时，箭头函数被执行，this 向上查找具有 this 的函数作用域，找到了全局作用域，所以，箭头函数的 this 指向 window，输出 “Alice”。

当使用 call 方法调用 test 时，离箭头函数最近的函数作用域，也就是 test 的 this 被硬绑定到 obj，所以箭头函数的 this 也指向了 obj，所以输出 Bob。

所以，对于箭头函数的 this 判断，需要忘记前面四种经验，单纯地从函数作用域链向上查找即可。

#### 6. 整体的判断思路

在以上的情况中，同时出现几种绑定情形时，该如何判断 this 的最终指向呢？这就涉及到这些绑定方式的优先级问题。

这几种绑定方式（除了箭头函数）的优先级如下：

new 绑定 > 硬绑定 > 隐式绑定 > 默认绑定

所以，遇到几种绑定方式同时出现的情况，我们可以按照如下的思路进行判断。

1. 函数是否是由 new 创建的实例调用。如果是，this 指向创建的新对象
2. 函数是否是显式绑定，也就是 call、apply调用，如果是，this 指向指定的新对象：`func.call(obj)`, this 指向 obj
3. 函数是否是隐式绑定，如果是，this 指向调用的上下文对象：`obj.func()`， this 指向 obj1
4. 如果不属于以上情况，则属于默认调用，this 绑定到全局对象（严格模式下，undefined）。

### 参考资料

- [JavaScript深入之重新认识箭头函数的this](https://muyiy.cn/blog/3/3.2.html)
- Book-你不知道的 JavaScript

---
title: React事件回调函数bind(this)解析
abbrlink: 574dfb59
date: 2018-07-27 18:23:25
tags: 
  - React
  - Javascript
---

在使用 React 的过程中（ES6 Class 语法下），我一直很疑惑一件事情，那就是事件的绑定，比如 onClick、onChange 的事件处理函数必须这样写

``` html
<button onClick={this.handleClick.bind(this)}>点击</button>
```

或者在 constructor 函数中声明

``` js
this.handleClick = this.handleClick.bind(this);
```


### 疑惑

为什么明明是使用的是 **this** 下面的函数，还要绑定 **bind** 一下 this 呢? 就好比『我想用自己的手机还要声明下这是我的手机』，不说写法是否正确，自我感觉代码的可读性和美观度都不佳

### 解析
Javascript 是一种比较特殊的语言，说起这个问题要从作用域谈起，首先，JavaScript 是只有静态作用域的，没有动态作用域，或许有人说 this 不是吗？NO，它只是像而已

顾名思义，静态作用域就是说作用域由你的代码书写位置决定的，而动态作用域的作用域是调用执行的时候确定的，没错 this 就是这样的

this 的作用就是找到函数被调用所绑定的位置，那么位置寻找是有规则的，下面是四条寻找规则

一、 默认绑定
默认绑定，顾名思义，就是无法应用其它规则的时候使用的绑定规则，这种规则也是函数中最常用的，叫做**独立函数调用**，例如：
``` js
function foo() { 
  console.log( this.a );
}

var a = 2; 

foo(); // 2
```

**foo** 不带任何修饰的函数引用进行调用，只能使用默认绑定规则
这时，**this** 的指向默认是全局作用域，即 **window** ,当然是在非严格模式下。在严格模式下就会是 **undefined**


二、 隐式绑定
还有一种情况，函数存在于对象中，被对象所引用，例如
``` js
function foo() { 
  console.log( this.a );
}
var obj = { 
  a: 2,
  foo: foo 
};

obj.foo(); // 2
```
这种情况 **this** 绑定的就是 obj， 因为函数存在于对象之中，并且被该对象所调用。
即使 foo 函数存在于对象内部，但有时也会找不到它的上下文，比如
``` js
function foo() { 
  console.log( this.a );
}
var obj = { 
  a: 2,
  foo: foo 
};
//将 foo 函数赋值给一个变量
var bar = obj.foo; 

bar(); // undefined
```
此时，函数 bar 是对 obj.foo 的一个引用，严格来说，跟对象 obj 没有任何关系，此时 bar 的执行就可以运用默认绑定规则，所以它的上下文this 指向 window 或者 undefined

还有一种情况，就是在回调函数中引用，也会出现找不到上下文，造成 this 绑定丢失

``` js
function foo() { 
  console.log( this.a );
}

var a = 'window a';

var obj = { 
  a: 'obj a',
  foo: foo 
};

// obj.foo 通过参数传递给 setTimeout 
setTimeout( obj.foo, 100 ); 

```
此时 this 绑定丢失, 就会应用默认绑定，找到 window

三、 显式绑定
显示绑定是通过 call(..) 和 apply(..) 方法，强制将 this 指向传入的对象，这种方式也叫做硬绑定

``` js
function foo(something) {
    console.log(this.a, something);
    return this.a + something;
}
var obj = {
    a: 2
};
var bar = function() {
    return foo.apply(obj, arguments);
};
var b = bar(3);

console.log( b ); // 5

```


四、new 绑定
new 通常是使用一个函数来构造一个对象，并且该函数中所指向的 this 会绑定在这个对象上，举个栗子 🌰
``` js
function foo(a) {
    this.a = a;
}
var bar = new foo(2);
console.log(bar.a); // 2
```
使用 new 来调用 foo(..) 时，我们会构造一个新对象 bar 并把它绑定到 foo(..) 调用中的 this 上

new 是最后一种可以影响函数调用时 this 绑定行为的方法，我们称之为 new 绑定



那么综上所述， React 这个情况就很好理解了

``` html
<button onClick={this.handleClick.bind(this)}>点击</button>
```

**this.handleClick 方法是通过回调函数传参执行的，而在 Class 语法中并没有默认做一个当前 this 绑定，所以会丢失 this 的绑定，在严格模式下，this 是 undefined**

Function.prototype.bind() 是函数自带的绑定上下文方法， 与 call(..) 和 apply(..) 功能相似，bind(this) 之后会创建一个新的函数，并且 this 绑定在当前想要的地方

React 文档对此问题描述：

**You have to be careful about the meaning of this in JSX callbacks. In JavaScript, class methods are not bound by default. If you forget to bind this.handleClick and pass it to onClick, this will be undefined when the function is actually called.**


### 替代
当然，this.handleClick.bind(this) 这种写法是有其它替代写法的，主要有两种

第一种，可以使用箭头函数，因为箭头函数是属于静态作用域的，所以 this 会直接绑定在当前作用域

``` js
 <button onClick={(e) => this.handleClick(e)}>
```

第二种，使用 [public class fields](https://babeljs.io/docs/en/babel-plugin-transform-class-properties/) 语法，这样就不需要每次绑定 this，可以直接使用，当然这个规范还在实验阶段，需要通过 babel进行编译执行


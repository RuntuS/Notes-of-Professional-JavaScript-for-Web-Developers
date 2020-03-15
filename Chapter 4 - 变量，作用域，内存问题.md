> 目录
[1. 本章概述](#本章概述)
[2. 基本类型和引用类型](#基本类型和引用类型)
&emsp;[2.1 构造函数 ->  基本类型转化为引用类型](#构造函数----基本类型转化为引用类型)
&emsp;[2.2 函数传参](#函数传参)
&emsp;[2.3 instanceof](#instanceof)
[3 执行环境和作用域](#执行环境和作用域)
&emsp;[3.1 作用域链](#作用域链)
&emsp;[3.2 整值提升](#整值提升)
&emsp;[3.3 补充: ES6块作用域，let于const声明](#补充-es6块作用域let于const声明)



# 本章概述
> 本章节涉及的内容非常的重要，作用域和内存问题是**闭包，this指针等重要内容的基础。**

# 基本类型和引用类型
* 在js中，引用类型只有Object，像Chapter 3中出现的简单数据类型**在没有被原生构造函数构造的时候**，都是基本类型 -> Undefined, Null , Boolean , Number , String.
* 引用类型的值是保存在内存中的**对象**。Javascript不允许直接访问内存中的位置。
* 要注意一点，**复制保存某个对象变量的时候，操作的是对象的引用，**但如果是为对象添加属性，则是直接对**对象进行操作**。
```javascript
var obj = {a : 1};
var new_obj = obj; // 对引用进行操作。
obj.c = 2; //直接对 对象进行操作
```

## 构造函数 ->  基本类型转化为引用类型
* 但是，如果对基本类型用对应原生函数进行构造，返回的结果会是对象。
```javascript
var c = new Number(21); //c是对象 Number <- 从Object继承的对象类型
d = c;
d.newProto = 2;
console.log(c); // [Number: 21] { newN: 2 }

```
* 可以发现，对d添加属性值，c也会收到影响。
* 同理String对象也是如此。

## 函数传参
* ES中所有函数的参数都是**按值传递**的。即函数传入的实参是对原来变量的拷贝。这无论对基本类型还是引用类型都是成立的。
* 只不过基本类型是直接拷贝的值，引用类型是拷贝的引用。
```javascript
function foo(x){
    console.log(x);
    if(x instanceof Object === true)
    {
        x.new_a = 1;
    }
    else
    {
        x = 10;
    }
}

let obj = {a : 1};
let num = 9;
foo(obj);
foo(num);
console.log(obj);
console.log(num);
/*
    { a: 1, new_a: 1 }
    9   
*/


```
* 可以注意的是，引用类型由于是传递引用的**复制**，所以**对对应堆内存引用是共享的**，自然函数内部的操作，可以体现到外部的对象变量上。而值由于是副本，所以自然不会改变。


## instanceof
* 关于instanceof，我在之前的博客提到过[原型链与继承](https://www.laolan-runtu.xyz/home/1/20200223%E7%BB%A7%E6%89%BF%E4%B8%8E%E5%8E%9F%E5%9E%8B%E9%93%BE)
* 所以在这里只是提一下。
* instanceof可以快速检测出是否为引用类型，对于一切的非引用类型（基本类型），instanceof返回结果一定是false，**这个原因和原型链有关，因为基本类型不存在原型链这一说法。**
```javascript
var c = 1213;
c instanceof Number; //false
```

* 对于引用类型，instantceof后如果是Object，那么一定为true，（这也和原型链有关，具体参考第六章)
* 所以如果要确定引用类型具体是谁，则要精确到Object的子类型上(Array,Function,RegExp等等)
```javascript
let arr = [12,3,4];
arr instanceof Array; // true

```
* 具体的instanceof原理请参照我写的那边博客.(上面有给出链接)


# 执行环境和作用域
* 在Js中，每一个执行环境都有一个与之关联的**变量对象**，比如浏览器的全局对象window对应的就是整个浏览器全局环境。环境中所定义的变量和函数都在这个大对象里，即通过这个对象可以引用环境内部的变量。
```javascript
var num = 10; //挂载到全局变量上
window.num; // 10

```

* 在ES5以前，JS中**没有类似于C语言的块作用域**，只有全局作用域和函数作用域。
* 每一个函数的作用域也是它本身的执行环境，一旦执行流遇到了函数执行，就会向**执行栈里添加该函数的执行环境。**

## 作用域链
* 作用域链本质和栈类似，上面从最左侧(最开始遇到的)到最右侧(当前环境下的)存储着各个环境下的变量。
* 最右侧的变量便是当前环境变量下的变量。
```javascript
var num = 10;
function foo(){
    var num = 20;
    function inFoo(){
        var num = 30;
        debugger
        console.log(num);
    }
    inFoo();
}

foo();
// 当函数执行到debugger停止时
// 作用域链为(针对num) window.num -> foo.num -> inFoo.num


```
* 当代码进行变量寻值时，是从作用域链的最右侧向左侧开始索引，遇到即停。
* 在上述例子中，打印的就是inFoo的值30。


## 整值提升
* 在js中，如果用var声明变量，那么会出现整值提升，即声明的变量会直接提升到整个作用域的前端。
```javascript
console.log(num); // undefined

var num = 10;


```
* 这可能有疑问，为什么num输出的是undefined而不是10？
* **因为提升的只有声明，初始化并没有提升，初始化依然会在执行流执行到指定位置的时候才执行。**

* 但是整值提升也会带来一些很诡异的结果。
```javascript
var num = 10;

function foo(){
    console.log(num);//undefined
    
    let num = 20;
}
foo();



```
* 上述代码，num并没有输出10，而是undefined。因为内部声明的num进行了整值提升，所以num的声明在console.log之前已经进行，并且是**作用域链**的最前端，这就导致了打印出来的值是foo函数环境内部的num值，并且是暂未初始化的值。

* **这看起来都非常的不爽，因为不符合我们平时的习惯，不过还好，ES6引进了块作用域**

## 补充: ES6块作用域，let于const声明
* 为了弥补ES5的不足，ES6做了很大的改进，那就是块作用域的产生。
* 总体来说，改变了以下几个方面:
1. 声明词可以有let与const，并且声明的变量具有块作用域的属性。
```javascript
if(true)
{
    var a = 10;
}

console.log(a); //10

if(true)
{
    let b = 10; 
}

console.log(b);// ReferenceError: b is not defined

```
* 使用let声明的变量生命周期只在if的括号里，只要离开就会被回收。

2. let与const声明的变量不具有整值提升。
```javascript
console.log(num); // ReferenceError: num is not defined

let num = 10;

```

3. 暂时性死区(temporal dead zone，TDZ)
* 在块作用域里，一个变量无论声明在哪（let或const）声明，那么它所在块作用域(包括函数作用域)**对于这个声明的变量而言对外完全封闭，外部的相同名称变量不会影响内部的变量。**
```javascript
let num = 10;
//使用{}显式声明一个块作用域
{
    console.log(num);//ReferenceError: Cannot access 'num' before initialization
    let num = 30;
}
```
* 这提示了，num已经被声明，但是不能在初始化之前读取。这有点类似整值提升，但又不完全是，整值提升不会管变量是否初始化。



* 总而言之，ES6里引入了块作用域后，js的编程变的规范了许多，在下面的变量声明里，我都尽量使用let声明。
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
* 未完待续
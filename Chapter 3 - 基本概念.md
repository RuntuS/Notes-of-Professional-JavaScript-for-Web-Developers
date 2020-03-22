> 本章主要讲述了Js中的一些基本概念，从变量，数据类型，运算符到函数，对象都有所提及。


-----
[1. 前提概述](#前提概述)\
[2. 标识符](#标识符)\
[3. 严格模式(use strict)](#严格模式use-strict)\
[4. 变量](#变量)\
&emsp;[4.1 未声明的危险](#未声明的危险)\
[5. 数据类型](#数据类型)\
&emsp;[5.1 Object.prototype.toString()](#objectprototypetostring)\
&emsp;[5.2 特殊的Undefined类型](#特殊的undefined类型)\
&emsp;[5.3 特殊的Null类型](#特殊的null类型)\
&emsp;[5.4 Boolean对象](#boolean对象)\
&emsp;[5.5 Number类型](#number类型)\
&emsp;[5.6 NaN](#nan)\
&emsp;&emsp;[5.6.1 数值范围](#数值范围)\
&emsp;&emsp;[5.6.2 数值转换](#数值转换)\
&emsp;[5.7 String类型](#string类型)\
&emsp;&emsp;[5.7.1 转化为字符串的方法1: toString()](#转化为字符串的方法1-tostring)\
&emsp;&emsp;[5.7.2 转换为字符串的方法2: String();](#转换为字符串的方法2-string)\
&emsp;[5.8 Object类型](#object类型)\
[6.操作符](#操作符)\
&emsp;[6.1 自增与自减操作符的强制转换](#自增与自减操作符的强制转换)\
&emsp;[6.2 逻辑运算在对象中的运用](#逻辑运算在对象中的运用)\
&emsp;[6.3 相等操作符](#相等操作符)\
[7. 语句](#语句)\
&emsp;[7.1 for-in 语句](#for-in-语句)\
&emsp;[7.2 label语句](#label语句)\
[8 函数Function](#函数function)\
&emsp;[8.1 参数传值arguments](#参数传值arguments)\
-----


#  前提概述
* 这里总结的笔记大多是ES3定义的语法规则，并且对ES5中的修改加一阐述。

# 标识符
* 在js中，标识符以**字母，下划线\_,或美元符号\$，数字组成。**
* 其中，标识符的开头**不能是数字**。
```javascript
var 123abc; // error
var abc123; // correct
```

* 虽然书上表示以驼峰式来命名比较好，但是就我个人感觉而言，驼峰式有时候在视觉上还是不怎么舒服，我更倾向于用\_当作间隔符。
```javascript
var theFirstPromise; //驼峰式
var the_first_promise; // 自己的习惯
//更倾向于第二种命名方式。
```

# 严格模式(use strict)
* 严格模式的存在使得js的语法更严谨，毕竟严谨总是好事，有时候能避免许多不必要的错误。

```javascript
{
    "use strict"
    a = 1; // 未声明变量，错误。
}

{
    a = 1;// a自动挂载到全局变量上。
}
```
* **use strict需要放在作用域的最前面。**

# 变量
* js中变量是**松散型**的，它可以用来保存任何数据，这意味者同一个变量在不同的时段可以存储不同的类型的值。
* 虽然这看起来确实很方便，但不推荐这么做，**毕竟一个变量在不停地变换类型总是会给人带来阅读代码的麻烦。** 为后来的调试会带来许多不必要的麻烦。
```javascript
var a = 12; // Number

a = "good"; // String

```

## 未声明的危险
* 刚刚提到，如果在非严格模式下直接使用一个**未声明**变量，那么它不会报错，而是直接挂载到全局变量上。(浏览器环境下的window，node环境下的global)。
* 这会污染到全局环境，这是不好的现象。**在全局环境下声明的变量都是有特殊命途的。**
* 所以一般声明变量都要求在块作用域里声明，关于作用域问题将在第四章提到。
```javascript
function foo() {
    var a = 2;
    console.log(a);
}
foo(); // 2
console.log(a);// undefined


// 不声明
function foo(){
    a = 2;
    console.log(a);
}

foo(); // 2
console.log(a); //2 环境被污染

```



# 数据类型
* 基本数据类型: Number,Boolean, Null, Undefined, **String**.

* 复杂数据类型: Object -> 对象(object) + 函数(Function) + 数组(Array)

* **typeof**可以检测数据的类型，它不是方法，只是和一个和**delete**类似的操作符。
```javascript
var str = "abc";
var num = 123;
var bool = true;
var unde = undefined;
var obj = {a : 1};
typeof str; // string
typeof num; // number
typeof bool; // boolean
typeof unde; // undefined
typeof obj ; // object
```
* 但是对于null和Object衍生对象，会稍微不一样。
```javascript
typeof null; // -> object ？？？？ 
```
* 这里会感到很奇怪，**其实这是个长久以来的bug**。
* 原因与底层的二进制编码有关，typeof后检测的数据类型如果对应的底层二进制编码开头为0，则会识别为object，但是null全部都为0，所以自然被识别为object。

* 对于Object衍生对象，其实就是Array，Function类的，准确来说，它们是Object类的子集，这里说为衍生对象，比较好描述。
```javascript
var arr = [1,2,3];
function foo(){}
typeof arr; // object
typeof foo; // object

```

* 发现函数和数组都被识别为object，这当然没问题，但是不能达到想要的结果。

* **这里自然有解决办法**
## Object.prototype.toString()
* 对于所有的Object，如果其**原型链上**的toString没有被**重写**，那么在调用toString方法的时候，就会自动转移到顶层Object原型链上的toString.
* Object.prototype.toString 的输出形式是 "[object type]"，通过**type**的值，我们就能知道对应的衍生对象是什么类型的。
```javascript
var arr = [1,2,3,4];
var foo = function(){};

console.log(Object.prototype.toString.call(arr)); // [object Array]
console.log(Object.prototype.toString.call(foo)); // [object Function]

```
* 解释一下，call的作用就是被toString的this对象指向要检测的变量。因为这里是直接调用Object原型链的方法，所以如果不通过this来转移，**那么该toString方法会默认其this对象为Object**。
```javascript
Object.prototype.toString();//[object Object]
```

* 显然，**通过Object.prototype.toString方法显然可以确定出具体的变量类型，但是为什么不直接去调用toString呢？**就像下面这样。
```javascript
foo.toString();// "function(){}"
arr.toString();// "[1,2,3,4]"
```
* 可以发现，直接调用的toString和Object上引用的toString结果是不一样的。
* 原因很简单，直接调用的toString和Object上的toString是不一样的。**Function 和 Array原型链上的toString是被重写过的。**(关于原型链，在第五章会提到)


## 特殊的Undefined类型
* Undefined类型只有一个值 -> undefined。
* undefined的出现有两种场景: 
1. 变量没有声明。
2. 对象内部不存在属性。
```javascript
var a;
var obj = {pro1: 1, pro2: 2}
console.log(a); //undefined
console.log(obj.pro3);//undefined
console.log(b); // error

```
* 不能直接打印未声明的变量，但是可以通过typeof获得未声明变量的“值”(这是毫无疑义的，这里只是为了方便理解)

```javascript
console.log(typeof d);//undefined
```

## 特殊的Null类型
* Null类型也只有一个值 -> null
* null值表示的是一个**空对象指针**，从这一方面来说，typeof null为object也说得通(即使它是个bug)。
* null一般用于给待赋值的对象，这样通过检查是否为null对象就可以知道该对象是否已经被赋值了。
```javascript
var obj = null;
//一个ajax请求，这里是伪代码。
obj = ajax("http://www.someurl.com");

if(obj !== null)
{
    ///.....做进一步的处理
}

```

* 注意一点，undefined在双等(相等)模式下和null判等是true，但是在全等(三等)是false。
```javascript
undefined == null; //true
undefined === null; // false
```

## Boolean对象
* 关于Boolean对象，说几个特殊的地方。
* true不一定等于1，false也不一定等于0，即使在双等模式下，它们确实会相等(因为存在强制转换，后面会提)。

* **Boolean函数**
* 通过Boolean函数，可以将任何数都转化为boolean对象。
* **除了false，""(空字符串),undefined,null,NAN**这五类值会被转化为false，其他都会被转化为true。


## Number类型
* js中的Number类型都是使用**IEEE 754**格式来表示的。
* 所以浮点数的相加并不是一件好事，因为某些浮点数转化为二进制是“无穷的”(类似于除法的除不尽。)
```javascript
//经典例子
0.1 + 0.2 ;// 0.30000000000000004

```
* 其次，Number类型里也有二进制，八进制，十进制和十六进制的表达。
* 二进制表示 -> 0bxxxxxx;
* 八进制表示 -> 0xxxxxxx;
* 十六进制 -> 0xyyyyy;
* **八进制要特殊说一下，老版本的八进制表示0xxxx在严格模型下会报错。**
* 查了下网上的说法，是出于普通人认知的问题(？？？其实我觉得0开头的自然就是八进制了)，比如077是63，078就变成了78了。
* 其实JS转换机制也能够理解为什么ES开发者会这也做。
* **如果字面量的值中的数值超出了范围(比如八进制中出现了8),那么前置0会被忽略掉，并且隐式转化为十进制表达。**
* 那么存在强制转换，自然，如果在数值不超过范围的前提下，前面因为有个0，就会出现歧义: 是八进制呢，还是十进制呢？毕竟两者都说得通。显然这种是不被允许的。
* **于是在新版本里，采用了0o的开头来描述八进制数(具体是哪一版本更新的我没查到，如果后面了解到会来此处更新)。**
* 在严格模式下，使用0b，**如果你把数写超了，不会自动转换，而是直接报错，消除二义性**。
```javascript
"use strict"
console.log(0o0000100);// 64
console.log(0o2345678);// Invalid or unexpected token

```


## NAN
* NAN(Not a Number): 是一个特殊的数值，**这个数值要返回数值的操作数但是实际并没有返回数值的情况(这也不会抛出错误).**
```javascript
var str = "youdiansao";
str / 2; //NAN 被应该返回数值，但是str是字符串，并且无法隐式强制转换，于是返回NAN
```

* NAN有两个特点: 1. 任何变量与NAN计算操作的结果，结果一定是NAN。 2.NAN不等于自身。
```javascript
NAN === NAN; //false

```

* **isNaN方法**，判断一个参数是否"不是数值"。类数字的也能判定为数值，比如"123" .
```javascript
isNaN(123); // false 意味着 不是不是数值 -> 是数值
isNaN("str");// true -> 是 不是数值 -> 不是数值
isNaN("123");// false 隐式强制转换
```


### 数值范围
* JS最小和最大的数值分别保存在 Number.MIN_VALUE 和 Number.MAX_VALUE中。
* 一旦超出这两个范围，如果是正数，则会被定义为 Infinity ，如果是负数，则被定义为 -Infinity。
```javascript
console.log(-Number.MAX_VALUE - Number.MAX_VALUE);// -Infinity
console.log(Number.MAX_VALUE + Number.MAX_VALUE);// Infinity
```

* 注意**任何数除以0都可以得到Infinity**

### 数值转换
* 涉及数值转换的方法有三个: Number(), parseInt(), parseFloat();
* Number() 适用于任何类型的转换。 而parseInt() 和 parseFloat()则专注于字符串的转换。
* 说下注意点，Number如果遇到不可转换的情况，则会生成NaN。
* parseInt和ParseFloat，如果它们扫到的第一个非空格字符不是数字或者负号，则会返回NaN。但是如果非数字字符出现在数字后面，则一样可以转换。



## String类型
* js中的String类型表示由**零个**或多个**16位Unicode字符**组成的字符序列 -> 即字符串。
* 可以用单引号包装，也可以用双引号包装，但是一单一双必会导致错误。
* **字符串的特点: ECMAScript规定字符串是不可变的。意味着字符串一旦创建，将不会被改变。**
```javascript
var str = "12345";
str[0] = "5";
console.log(str); // 12345

```

* 而对于字符串的"+"运算，原理则是引擎先创建一个能容纳两个字符串的空间，然后把两字符串存进去后，再销毁原字符串。
```javascript
var str1 = "good";
var str2 = " morning!";
var str = str1 + str2 ; //"good morning!";

```

### 转化为字符串的方法1: toString()
* 之前提到过，**几乎所有的对象**都拥有toString方法，通过toString，能把变量以字符串的形式展示出来。
* **并且由于重写的原因，不同对象类型的toString方法可能是不一样的。**
* Number的toString方法 -> 传入的参数表示将数字对象类型的十进制数转化为相应的进制数。
```javascript
var num = 10;
num.toString(2);// "1010"
num.toString(8);// "12"
num.toString();// "10" -> 缺省状态是十进制
num.toString(16); // "a"
```

* 这可能有个疑问，num明明是基本数据类型，并不是对象，为什么可以使用toString方法？
* 其实这里的num在使用toString之前，已经隐式地被**Number构造函数**给封装了，封装后的num表现为**Number对象类型**。
* 而他们使用的toString方法，其实都是在Number.prototype上定义的。
* 如果显式来看，可以理解为下面的方式。
```javascript
var num = new Number(10);
num.toString(2); // "1010"
```
* 拓展开来，不仅仅是number，比如基本数据类型**string**的道理是同样的，string本身是没有toString方法，**是由于被String构造函数隐式转换后，才会具有对应的方法。**

### 转换为字符串的方法2: String();
* String与Number类似，适用于任何的数据类型，包括undefined和null，都可以直接转换为其对应的字符串类型。
```javascript
console.log(String(null)); //"null"
console.log(String(undefined));// "undefined"

```


## Object类型
* Object对象是一组数据和功能的集合，在Javascript也是最重要的数据结构之一。
* 它可以用类似与Java中构造方式对象来构造，也可以直接**通过构造字面量的方式构造**。
* 我本人而言更倾向于第二种，因为更直观更方便。
```javascript
// 构造对象
var obj = new Object();
obj.a = 1;
obj.b = 2;


// 字面量构造
var obj = {
    a : 1,
    b : 2
}



```

* 对象一定会涉及原型链，其上涉及大量的原生函数，这将会将在第五章原型链中慢慢提及。

# 操作符
> js的操作符和其他语言(比如C，Java)中的基本一致，这里不会谈及基础部分，只会说说不一样的地方。
## 自增与自减操作符的强制转换
* 有时候利用这一点来进行强制转换会显得非常神奇。下面有个例子。
```javascript
var str_num = "12345";
var type = typeof (++str_num);
str_num; // 12346 -> number类型
type; // number

```
* **居然可以对字符串进行自增!**其实它悄悄地调用Number()对str_num进行强制转换，如果说，str_num里面符合类数字的要求，那么就会成功转换；如果不能，则会返回NaN。
```javascript
var str = "abc";
typeof (++str);// NaN

```

## 逻辑运算在对象中的运用
* 一样的逻辑运算：与，或，非。的用法和其他语言也是一样的。**这里主要谈一下逻辑判断在对象中的运用。**
* **逻辑与**
```javascript
var obj1 = {a : 1};
var obj2 = {c : 1, d : 2};
console.log( true && obj1);// obj1
console.log( null && obj1); //null
console.log(obj1 && undefined); //undefined
console.log(obj1 && obj2); // {c: 1, d: 2};

```
* 其实这个也很容易理解，当对象和一个具有逻辑属性的进行逻辑判断时，当且仅当逻辑属性为true的时候，整个表达式才会返回对象自身。 
* 如果逻辑属性具有“空”属性，那么就会造成短路，返回的一定是空属性。
* **如果两个都是对象，则返回第二个对象。**

* **逻辑或**
* 相比逻辑与，逻辑或在实战中运用的比较多一些。
```javascript
var obj1 = {a : 1};
var obj2 = {b : 2};
console.log(false || obj1); // obj1
console.log(obj1 || obj2); // obj1
```
* 当且仅当第一个式子为false的时候，才会返回第二个对象。
* 如果是两个对象进行逻辑运算，且都是具有实际的值，那么一定返回第一个对象。
* 具体的运用可在node开发中使用，**判断是否处于生产环境中而进行端口的变动号**。

## 相等操作符
* 前面有提到过，js中涉及全等(===)和相等(==)两种情况
* 全等是最保险的，也是必须的。它能提供本来就不怎么严谨的js以严谨性。因为相等会存在隐式强制转换，多多少少会存在一些问题。


# 语句
> 像if，while，for类似基础语句这里不会谈及
## for-in 语句
* for-in是一种精准的迭代语句，它可以**枚举出**对象中的**可枚举属性**。方便于遍历对象内部。
```javascript
var obj = { a : 1, b : 2 };
for (i in obj)
{
    console.log(`${i} : ${obj[i]}`);
}

// a : 1
// b : 2
```
* 注意，枚举的值是**属性的名称**，而不是属性的值。
* 其次，如果对象中存在不可枚举的属性，那么不会被for in遍历到.
```javascript
var obj = {a : 1};
Object.defineProperty(obj,"b",{
    value : 2,
    enumerable : false  //属性描述符，定义不可枚举
});

for (i in obj)
{
    console.log(`${i} : ${obj[i]}`);
}

// a : 1
```

## label语句
* label语句常用语多层循环跳出，通过label标识代码位置。
```javascript

outermost : 

for(...)
{
    for(...)
    {
        for(...)
        {
            if(...)
            {
                break outermost;
            }
        }
    }
}

```
* 只要满足if条件，执行可以直接跳到最外层outermost处，而不用一层一层地往外跳。



# 函数Function
> 函数本身就是一个对象，于是在js中，函数可以直接使用function声明，也可以使用赋值的方式创建函数。
```javascript
//方法1
function foo(){};

//方法2
var foo = function(){}; //右侧实则为一个匿名函数

```
* 其次js在调用函数时，没有硬性规定传入的参数一定要和定义参数一致，多出的参数被丢弃，**少的参数被赋值为undefined**。
* 有时候，我们想做到类似于Java中方法重载的效果(因为js中没有函数签名，所以说没有函数重载这一设定)，但是多出的参数说丢就丢，需要想个办法来存储传入的参数。
* arguments对象能解决这个问题。

## 参数传值arguments
* 任何的函数内部，都有一个名为arguments的**类数组对象。**(为什么说是类数组，因为它并不是完全的Array类型，但是又可以用部分Array的方法)
* 而这个类数组存储的是传入的参数。
```javascript
function foo(arg1,arg2)
{
    console.log(arguments.length);
}

foo(1,2,3);// 3
foo(1,2);//2
foo(1);//1
foo(); //0
```
* 于是通过arguments的长度，我们可以做到类似方法重载的效果。
```javascript
function foo()
{
    if(arguments.length === 0)
    {
        ...
    }
    else if(arguments.length === 1)
    {
        ...
    }
    else
    {
        ...
    }
    
}

```



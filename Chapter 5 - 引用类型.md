> 这一章将展开说明Javascript中的引用类型 不止是Object，更多的是Object的自类型 Array, Function等




# Object
-------------

* Object对象是js中最常用的一个数据结构，同时也位于原型链的最顶端(第六章会讲到)，即和Java类似，所有的对象都是由Object衍生而来的。
* Object有两种构造方式: 构造函数构造 和 **对象字面量构造**.
* 其中，后者比较常用。
```js
//构造函数构造
var obj1 = new Object(); 
obj.a = 1;       

//对象字面量构造
var obj2 = {
    a : 1    //这种比较体现封装性
}
```

* 对象的属性名**一定是字符串**，如果不是字符串，会进行强制转换。
```js
var obj = {
    1 : "a"
}

for ( i in obj)
{
    console.log(typeof i); //string
}
```

## Object属性引用
------------
* 引用属性值的方法有两种：
1. 通过符号“\.”引用。
2. 通过数组方式引用，即"['prototypeName'\]"

* 一般来说，都用第一种方法来引用，比较直观。
* **但如果说生命的对象名称里面包含非法的变量字符(比如 - ，。等），那么只能通过第二种方式来获得属性值**

<br />

* 但这可能有疑问了，既然拥有非法字符，那么声明的时候怎么可能成功呢？
* 其实这是相对的，既然只能通过数组方式来引用，那么也只能通过数组方式来声明，如下。
```js
var obj = {
    ["_sd2123---"] : "123"
}
console.log(obj); //{ '_sd2123---': '123' }
console.log(obj["_sd2123---"]) //123
```
* **可以看出如果名称包含非法字符，那么声明的时候也必须加中括号。**

* 同时，通过数组方式来引用还有一个好处，那就是**可以通过变量去访问对象属性，灵活度更高。**
```js
let obj = {
    "proto_name":"yes!"
}

let proto = "proto_name";
console.log(obj[proto]); // yes!
```

# Array类型
* 数组也是js常用的数据结构，并且在js中数组是**动态的，即你不需要去提前规划好数组的长度。同时也不需要考虑一个数组里面是否装同一个类型变量(但是原则上还是要这样做）**
* 数组声明和Object一样，有两种形式:
1. 构造函数式。
2. 数组字面量式。
```js
//构造函数
let arr1 = new Array(20);//规定长度，但是是可变的。
let color = new Array("red","blue","yellow");// 也可以提前指定数组元素

```

* 由于是动态数组，所以数组的长度是根据**下标最大**的非undefined元素来确定的。
```js
let arr = [];//arr.length = 0
arr[10000] = 0; // arr.length = 10001;
```
* 即使前1w个元素是空的，但10001个元素有值，那么长度就以其为标准。
* 所以数组元素的加入一般通过**栈(push)或队列(shift)的方式加入。**

## Array.push ， Array.pop ， concat方法
* push和pop是数组栈操作，通过对**数组尾部**进行推入和弹出。
```js
let arr = [];
arr.push("1");//["1"]
arr.push("2");//["1","2"]
let str = arr.pop();
str; //2
arr; // ["1"] 
```
* **特别要注意，这两个方法都不会返回一个新数组(深受map的毒害，经常写着写着就赋值了）**,push返回的值是push后数组的长度。
* **但是push依然有弱点，它不能批次传入，比如说我们想把一大坨数据全部塞到一个数组里，如果用push，就要通过循环一个一个地推进去。**
* 为了代码的美观，可以考虑将数据放在一个数组里，然后一起“推入”，这可以采用concat方法。而且concat方法还有一个好处，**它可以返回一个新数组，和原来那个数组毫无关联**
* 况且，concat也能传递单个参数(非数组变量)，它也可以做到push的事情，唯一不同的是它返回一个原数组的副本。
```js
let arr1 = [1,2,3,4];
let arr2 = [2,3,4,5];
let new_arr = arr1.concat(arr2); //[1,2,3,4,2,3,4,5]
let new_arr_2 = arr2.concat(3); // [2,3,4,5,3]
```

## Array.shift()
* shift方法是弹出数组头部的元素。**shift + push**可以构成数组的队列操作(FIFO)
* shift对应的还有unshift，unshift则是把元素从数组的头部推入。
```js
let arr = [1,2,3,4]
arr.shift();// 返回值1
arr.unshift(5);//[5,2,3,4];

```

## 数组长度可读可写
* 数组由于是动态的，所以数组长度可写的。
```js
let arr = [];
arr.length; // 0
arr.length = 5;
console.log(arr); //Array(5) []
```
* 即可以动态改变数组长度来决定数组的存储大小。**为了保证严谨性，除非必要，都不要这样做。很容易造成误解。**


## 检测数组 Array.isArray
* 通过isArray可以快速检测数组。
* 或者像第三章所说，用Object.prototype.toString也可以进行检测。
```js
let arr = [1,2,3];
Array.isArray(arr);//true

```


## Array.toString 与 valueOf
* 在前面提到过，Array的toString是重写过的，所以结果和Object中的toString是不一样的。
```js
let arr = [2,3,3,4];
console.log(arr.toString()); // "2,3,3,4"
```
* Array.toString方法返回各个元素顺序排列并且用逗号进行分割的字符串。

* valueOf就是返回**原本的数组**，注意并没有生成新的数组，只是生成了一个新的引用，但是两个引用的是同一块内存区域。
```js
let arr = [12,3];
let newArr = arr.valueOf();
newArr.push(4);
console.log(arr);  // [12,3,4]
console.log(newArr); // [12,3,4]
```


## join方法 Array -> String
* join方法是非常常用的方法，它返回的是数组各个元素拼接成的字符串，中间的分割符号由自己定义。
```js
let arr = [1,2,3,4,5];
arr.join("-"); //1-2-3-4-5;
arr.join(""); // 12345  -> 数组转字符串的最方便的方法
```

* **注意，以上的方法，如果数组元素为null或者undefined的时候，返回的都是空字符。**


## 数组排序 sort()
* 数组排序方法也是很常用的。缺省状态时,是采用**依次判断大小来进行升序排列。**这个可能有点抽象，下面有个例子。
```js
let arr = [4,5,1,3,7,2,45,6];

console.log(arr.sort()); // Array(8) [1, 2, 3, 4, 45, 5, 6, 7]

```
* 看是不是很神奇，45居然在4和5之间，其实默认状态下，数字判断和字符串类似，都是从判断**从左到右第一个位开始进行升序排列。**

* **这显然是不行的，我们需要自定义匿名函数去实现排序。**
* sort可接受一个能接受两个参数的函数，两个参数代表前一个值和后一个值。
* sort通过返回值来确定本次比较值的大小，如果返回负数，**则前一个值在左侧，后一个值在右侧。**如果返回正数，则前一个值在右侧，后一个值在左侧。
* 下面这个例子就是升序排列的简单写法。
```js
let arr = [4,5,1,3,7,2,45,6];
arr.sort((value1,value2) => { 
    if(value1 < value2)
    {
        return -1;
    }
    else if(value1 === value2)
    {
        return 0;
    }
    else
    {
        return 1;
    }
})
console.log(arr);//// Array(8) [1, 2, 3, 4, 5, 6, 7, 45]
```

## 迭代方法 -> 这个很重要，也是在实际开发中很常用的。
### 1. filter() -> 过滤器 ->  ES5
* filter的作用非常强大，**它可以根据每次遍历返回的值来过滤出自己想要的元素,true就留下，false就舍弃。**。
* 注意filter不会对原数组进行改动。
```js
let arr = [2,4,5,6,7,8];

let newArr = arr.filter((item,index,array) => {
    if(item > 4)
    {
        return true;
    }
    else
    {
        return false;
    }
})

console.log(newArr);//[5,6,7,8]

```

### 2. forEach() ES5
* 这个方法和for去遍历数组是一样的，都是循环遍历一次数组的内容，没有任何返回值。
* 回调参数依然有三个，item,index,array.

### 3. map() ES5
* map是我个人觉得很好用的方法，**当想对数组的每个都进行一定的操作时，可以使用map来进行。**
```js
let arr = [1,2,3,4];
let new_arr = arr.map((item,index,array) => {
    return item*2;
})
console.log(new_arr);//[2,4,6,8] 

```
* 比如上述例子，可以对每个元素进行加倍操作。

## Array后续
* 数组还有许许多多的方法，像slice和splice这样的。但是不可能全部记录，应该是需要用再去用。
* 具体的可以查看MDN文档。
[splice具体用法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)


# Date类型
* 关于Date类型，主要用在时间的操作上，实际运用的比较少，个人觉得这个没必要去进行专门的学习，需要用的时候再去查。
[Date时间类型](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Date)


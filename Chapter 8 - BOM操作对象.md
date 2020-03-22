> BOM对象 -> Browser Object Model 浏览器对象模型 顾名思义，其主要功能是控制浏览器的属性

-------
[1. window](#1-window)  
&nbsp; [1.1. Configurable导致的不可删除性](#11-\configurable导致的不可删除性)  
&nbsp; [1.2. 关于窗口frame -> 补充: frame标签已经废弃](#12-关于窗口frame---补充-frame标签已经废弃)  
&nbsp; [1.3. window获得浏览器窗口位置属性](#13-window获得浏览器窗口位置属性)  
&nbsp; [1.4. 窗口大小](#14-窗口大小)  
&nbsp; &nbsp; [1.4.1. document](#141-document)  
&nbsp; [1.5. 导航与打开窗口](#15-导航与打开窗口)  
&nbsp; [1.6. setTimeout与setInterval](#16-settimeout与setinterval)  
&nbsp; [1.7. 对话框](#17-对话框)  
[2. location](#2-location)  
&nbsp; [2.1. search与href](#21-search与href)  
&nbsp; [2.2. 跳转 -> assign 操作](#22-跳转---assign-操作)  
&nbsp; [2.3. 重新记载reload](#23-重新记载reload)  
[3. screen对象](#3-screen对象)  
[4. history对象](#4-history对象)  
&nbsp; [4.1. go,back,forward](#41-gobackforward)  
-------


* 在BOM里，最重要的是window对象。

# 1. window
* 书解释: **window对象有双重角色，它既是通过Javascript访问浏览器窗口的一个接口，又是ECMAScript规定的Global对象。**
* window是一个全局变量，在浏览器的任何位置都可以使用它。
* 所以只要是在全局作用域里设置的一切变量和函数，都会自动的作为**window对象的属性**。
```js
let age = 21;
function sayAge(){
    console.log(age);
}
window.age; // 21
window.sayAge();// 21
```

## 1.1. Configurable导致的不可删除性
* 如果是通过window定义的全局变量是可以通过**delete**关键字删除的，但是若直接在全局作用域下声明而挂在的window变量是不能被delete删除的，**因为它的configurable属性默认为false。**
* **configurable只要是false，则永远不会被改回来，即这是个单向操作。**
```js
//属性挂载
window.a = 21;
delete window.a;// true 删除成功

//声明
let b = 21;
delete window.b; // false 删除失败
```
* 同时利用window这个性质，可以简单的检测全局变量下是否声明过某个变量。
* **直接使用未定义变量会报错，但是如果直接使用为定义的对象属性不会报错，而是返回undefined**
```js
//判断a是否存在
console.log(a); // ReferenceError: a is not defined
console.log(window.a); // undefined
```

## 1.2. 关于窗口frame -> 补充: frame标签已经废弃
* frame在实际情况中貌似用的不怎么多(至少我现在还没有遇到过需要用的场景)。
* window.frames属性是可以直接控制框架属性的。这里不展开讨论。
* [MDN-Frame开发文档](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/frame)

* **frame已经从Web标准中删除，尽量不要使用该属性。**


## 1.3. window获得浏览器窗口位置属性
* window可以通过四个属性获得**浏览器在显示屏中的坐标信息**
* **本来说，frame没被废除的话，上面的说法是不对的，但是如今frame标签已经被废除，所以可以直接说这四个属性是获得浏览器的坐标信息（即最外层框架）。**
```js
window.screenX;  // 左侧距离
window.screenY;  // 上侧距离
window.screenLeft; // 左侧距离
window.screenTop; // 上侧距离
```
* 为何有两种不同的属性来表示同一种性质呢？ 这个可能要考虑到浏览器的不同支持问题。
* 在前几年的时候，firefox ， chrome ， IE， Safari各自支持属性的不一样。所以出现了这种情况。
* **不过0202年了，浏览器都能互相支持彼此的属性了。(至少Chrome和firefox是这样的)**
* 意思是前面每组属性用哪个都可以。

* moveTo与moveBy则是改变浏览器位置。该属性用处不大，这里不展开讨论。
[MDN-moveTo](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/moveTo)


## 1.4. 窗口大小
* window也可以获得浏览器的宽与高以及内部视图的宽与高。
```js
window.innerWidth; //内部视图宽度
window.outerWidth; //浏览器宽度
window.innerHeight; //外部视图宽度
window.outerHeight; //外部视图高度

```
* 书上说:**"Chrome下的outer和inner表示的都是视图属性"。**
* 但如今这个已经改变，**Chrome下这两个属性表示的不是同一种视图性质，而是和上述备注的一样。**

### 1.4.1. document
* document是window下的一个属性，**但是它也可以直接在全局作用域下被引用。**
```js
window.document === document; // true
```
* document本身是DOM操作的关键对象，这里提出来是因为它本身也具有浏览器视图性质。
```js
document.documentElement.clientWidth; //可视浏览器界面宽度
document.documentElement.clientHeight;//可视浏览器界面高度
//chrome下测试
document.documentElement.clientHeight === window.innerHeight;// true
document.documentElement.clientWidth  === window.innerWidth; //true
```
* 所以具体使用哪个，是可以根据自己的喜好来选择。


## 1.5. 导航与打开窗口
* window.open() 
* 我个人不建议使用这种方式来打开一个新窗口。并且通过window.open来打开新窗口会被**浏览器拦截。**
* 新的打开窗口方式后面会介绍。


## 1.6. setTimeout与setInterval
* 关于setTimeout我在我的博客里介绍过，牵扯到异步与任务队列，这里不过多介绍。
* 这里主要介绍较为不常用的setInterval.
* setInterval作用是设定定时器，每隔一个定时器时间执行一次内部的代码。
* 这也是个异步执行的过程。
```js
setInterval(()=>{
    console.log(1); //每隔1s执行一次
},1000)
```
* 无论是setInterval还是setTimeout，它们执行都会返回一个**唯一标识符**,类似于进程的pid，方便于后期的某个时间能够及时中断它。
```js
//引用书上一个例子
let num = 0;
let max = 10;
let interval_id = null;
function incrementNumber(){
    num++;
    if(num === max)
    {
        clearInterval(interval_id); //执行十次终止interval
        //在setTimeout里对应终止操作是clearTimeout
        console.log("Done");
    }
}

interval_id = setInterval(incrementNumber,1000); //获取id

```
* 不过有时候Interval可以用setTimeout来模拟。
```js
//上述例子的setTimeout模拟
function incrementNumber(){
    num++;
    if(num < max)
    {
        setTimeout(incermentNumber,1000);//只要没有达到最大值，就继续延迟执行
    }
    else
    {
        console.log("Done");
    }
}

setTimeout(incrementNumber,1000);

```
* 使用setTimeout可以不使用clear方法来终止执行。

## 1.7. 对话框
* 三种对话框。
1. alert()
2. confirm()
3. prompt()

* 1 -> alert警告信息，弹出警告拦告知用户某些信息。
* 2 -> confrim弹出选择拦（其中包含 yes 与 no），如果选择yes则方法返回true；如果选择no，则方法返回no。
* 3 -> prompt用来接受用户输入的信息。它有两个参数，第一个参数传入提示字符串，第二个参数传入输入框缺省字符串。
* **prompt要注意，如果用户不填写信息但是按确定，则方法返""空字符串。但是如果直接选择取消，则方法会返回null。**

* **上述三种方法都是同步执行，即它们在执行的时候，其他代码都会停止运行直到执行完毕后，其他代码才继续执行。**


# 2. location
* location和document类似，**包括下面要替代的其他两种全局变量**，它们都是window下的直接属性，但是又可以在全局作用下直接调用。
```js
window.location === location;  // true
```
* location对象主要内容是**存储搜索路径(路由)的有关信息。**在一定程度上，它是非常重要的对象。


## 2.1. search与href
* search属性返回路径中的**查询字符串，即问号后的部分。**
```js
// 当前路径 https://www.someurl.com/home/page/1?id=123&name=sao
location.search; // 返回字符串 "?id=123&name=sao"
//注意问号也会返回
```
* 所以如果要拿到具体的参数，则需要对返回的字符串进行过滤操作。
```js
//提供一种思路但不局限一种方法
function getQuery(){
    let query_str = location.search.length > 0 ? location.search.substring(1) : "" ; //去除第一个问号
    let query_arr = query_str ? query_str.split("&") : []; // 把每组参数分开，用数组存储。
    
    if(query_arr.length === 0)
    {
        return [];
    }
    else
    {
        let queryObj = {};
        query_arr.forEach(item => {
            let mid_arr = item.split("="); //把每组参数分离
            queryObj[decodeURIComponent(mid_arr[0])] = decodeURIComponent(mid_arr[1]); // 对象赋值
        });
        return queryObj; 
    }
}

let query_obj = getQuery();
```
* decodeURIComponent是解码的功能，如果路径中包含非ASCII码的字符，则会被自动转换为utf-8编码(比如中文就是很好的例子)，于是需要调用该方法进行解码。
* 对应的编码函数为encodeURIComponent()


## 2.2. 跳转 -> assign 操作
* 我在前面提到过，尽量不要用open来打开网页，因为有时会被拦截，但如果是使用assign属性，则不会出现这种情况。
* 通过传递一个url给assign函数，则可以立即**打开新URL值，并在浏览器中生成一条记录。**即你可以点击返回键返回到上次浏览页面。
```js
location.assign("https://somepage.com"); //跳转到对应页面
```
* **如果我们显示的去修改window.location和location.href的值，则会自动执行assign函数。**
* 其中location.href返回的是当前页面完整的url。
```js
window.location = "https://somepage.com";
location.href = "https://somepage.com";
//上面两种方式都可以达到同种效果，其中href是常用属性
```

* **location对象其他属性也是差不多这个道理，如果修改属性值，都要自动跳转到修改后的路径页面上。**
* **由于本人比较懒，所以直接把书的几个例子直接复制过来。**
```js
//假设初始 URL 为 http://www.wrox.com/WileyCDA/
//将 URL 修改为"http://www.wrox.com/WileyCDA/#section1"
location.hash = "#section1";
// 将 URL 修改为"http://www.wrox.com/WileyCDA/?q=javascript"
location.search = "?q=javascript";
//将 URL 修改为"http://www.yahoo.com/WileyCDA/" 
location.hostname = "www.yahoo.com";
//将 URL 修改为"http://www.yahoo.com/mydir/" 
location.pathname = "mydir";
//将 URL 修改为"http://www.yahoo.com:8080/WileyCDA/" 
location.port = 8080;


```

## 2.3. 重新记载reload
* location.reload重新刷新页面。
* 缺省状态下，它可能从缓存中加载，当然也可以通过传入true来重新从服务器加载。
```js
location.reload();//普通刷新
location.reload(true);// 从服务器重新刷新

```
# 3. screen对象
* 这又来一个浏览器属性对象，它也是window下的一个直接属性，并且可以直接调用。
```js
screen === window.screen; //true
```

* 其实一般来说，上面的几种浏览器属性已经够用了，screen却显得比较多余。
* 所以这里不做介绍.

# 4. history对象
* 关于history对象，可以说它与**vue中的router息息相关。**
* 它可以保存用户上网的历史记录，通过也可以通过它来进行路由跳转。
```js
window.history === history; //老本行
```
* 前面我们通过location.href过滤来获得对应参数。但是在history里却有现存的。
```js
history.state.params; // 搜索参数存储在该对象内部
```

## 4.1. go,back,forward
* 通过history.go可以实现跳转。
* 它既可以通过历史记录相对跳转，也可以直接跳转。
```js
history.go(-1); //返回上一个页面，等同于按浏览器回退键
history.go(-2); //返回上上页面
history.go(1); //前往下一个页面，等同于按浏览器下页跳转键
history.go("https://baidu.com");//直接跳转到百度
```
* 为了方便，还具有history.forward()和history.back() 它们是go属性的特殊情况，代表着向前和向后跳转一个页面。
# 	JS 基础

![img](https://uploadfiles.nowcoder.com/images/20201126/5609248_1606381893258_B08CD4650CB2A7F5BB4DB9B09C50AA58)



前言：不整虚的，直接上干货：
前端基础知识：
(1)css的盒模型(老生常谈)，BFC的理解，选择器,层级上下文，三栏布局多种实现(position,flex,float等)，自适应布局rem原理(如何兼容不同手机dpi)，font-size10px如何实现、移动端一像素、媒体查询等等比较基础的问题，都是知识点。
(2)html方面基本问很少，这个重要程度没什么, 也就是一些标签语义化理解，和h5新特性，storage/cookie
(3) js这个是重点，会从基础去考察。 从浏览器返回html到渲染出页面，再到中间涉及到的优化点。
DOM和css如何解析，如何渲染出元素？
回流和重排怎么优化？
js为什么需要放在body(更好的回答其实是浏览器的渲染引擎和js解析引擎的冲突，当然回答js是单线程执行也没问题,如何优化)？
操作DOM为什么是昂贵的？
js如何执行(even Loop/宏任务、微任务，事件队列，promise,async/await)？
js的作用域？
闭包的理解(防抖和节流)？
(通过一些题进行考察)，基础类型以及如何判断类型？
事件机制以及如何实现一个事件队列？
oop编程和原型链？
最优的继承方式，es6 super的作用（进阶），this指向问题和new的过程(bind函数&&new函数手写)？
js深拷贝？（JSON方法实现拷贝有什么问题？）
掌握如上基本可以横行了，如何霸道呢，那就是框架和打包工具的使用和原理知识了~后续详解
透漏几个面试小技巧
（1）简历写的贼**，看了简历各种框架会用，什么webpack/vue全家桶、react全家桶、rollup/node都有，一问基础就凉了。 框架的底层还是js基础，基础不扎实，面试两行泪。
（2）简历的技术点要写自己擅长的，面试一妹子，2年工作经验，写着深刻了解vue原理，一个问题nextTick是怎么可以获取到更新后的DOM的，很简单，不知道~当然会扣分。问基础很多不知道，凉~
（3）面试要诚实，不可以浮躁，不会一些知识点也没什么问题。一精神小伙，问rem响应式布局原理，js判断怎么实现的，不知道~_~,问我可不可跳过这个题，最近没怎么看。 我：最近在看哪方面？jsxh:前端工程化东西？ 我：心里想很浮躁~，说一下common.js/es6模块化方案的不同？多个项目文件共用nodeModules如何做工作区间？如何监听git提交？ts解决哪些问题？ 凉 工程化是个很大的一个问题，从开发，编译，部署，上线都是有很多的点
（4）项目说的很到位，手写一个节流emmm,手写一个深拷贝emmm,手写一个promise.all，emmmm~~
(5）遇到几个不错的候选人，虽然一些知识~~点和手写代码能力差一些，人很靠谱很nice，看到了以前初级开发工程师的我，我会给机会通过,不是技术会把人卡的死死的。

总结下来基础和手写代码能力很重要~很重要~很重要~，框架做的再好底层也是基于基础去做的，整个了各种知识点、设计模式等。至于框架问哪些问题，如何准备，下回分解，完了甩你们一些链接。





## 一、执行上下文/作用域链/闭包

### 1. 执行上下文

如果要问到 JavaScript 代码执行顺序的话，想必写过 JavaScript 的开发者都会有个直观的印象，那就是顺序执行，毕竟：

```javascript
var foo = function () {

    console.log('foo1');

}

foo();  // foo1

var foo = function () {

    console.log('foo2');

}

foo(); // foo2
```

然而去看这段代码：

```javascript
function foo() {

    console.log('foo1');

}

foo();  // foo2

function foo() {

    console.log('foo2');

}

foo(); // foo2
```

打印的结果却是两个 `foo2`。

刷过面试题的都知道这是因为 JavaScript 引擎并非一行一行地分析和执行程序，而是一段一段地分析执行。当执行一段代码的时候，会进行一个“准备工作”，比如第一个例子中的变量提升，和第二个例子中的函数提升。

但是本文真正想让大家思考的是：这个“一段一段”中的“段”究竟是怎么划分的呢？

到底JavaScript引擎遇到一段怎样的代码时才会做“准备工作”呢？

**可执行代码**

这就要说到 JavaScript 的可执行代码(executable code)的类型有哪些了？

其实很简单，就三种，全局代码、函数代码、eval代码。

举个例子，当执行到一个函数的时候，就会进行准备工作，这里的“准备工作”，让我们用个更专业一点的说法，就叫做"执行上下文(execution context)"。

**执行上下文**

接下来问题来了，我们写的函数多了去了，如何管理创建的那么多执行上下文呢？

所以 JavaScript 引擎创建了执行上下文栈（Execution context stack，ECS）来管理执行上下文

为了模拟执行上下文栈的行为，让我们定义执行上下文栈是一个数组：

```
ECStack = [];
```

试想当 JavaScript 开始要解释执行代码的时候，最先遇到的就是全局代码，所以初始化的时候首先就会向执行上下文栈压入一个全局执行上下文，我们用 globalContext 表示它，并且只有当整个应用程序结束的时候，ECStack 才会被清空，所以程序结束之前， ECStack 最底部永远有个 globalContext：

```javascript
ECStack = [
    globalContext
];
```

现在 JavaScript 遇到下面的这段代码了：

```javascript
function fun3() {
    console.log('fun3')
}

function fun2() {
    fun3();
}

function fun1() {
    fun2();
}

fun1();
```

当执行一个函数的时候，就会创建一个执行上下文，并且压入执行上下文栈，当函数执行完毕的时候，就会将函数的执行上下文从栈中弹出。知道了这样的工作原理，让我们来看看如何处理上面这段代码：

```javascript
// 伪代码

// fun1()
ECStack.push(<fun1> functionContext);

// fun1中竟然调用了fun2，还要创建fun2的执行上下文
ECStack.push(<fun2> functionContext);

// 擦，fun2还调用了fun3！
ECStack.push(<fun3> functionContext);

// fun3执行完毕
ECStack.pop();

// fun2执行完毕
ECStack.pop();

// fun1执行完毕
ECStack.pop();

// javascript接着执行下面的代码，但是ECStack底层永远有个globalContext
```

**思考题**

好啦，现在我们已经了解了执行上下文栈是如何处理执行上下文的，所以让我们看看上篇文章[《JavaScript深入之词法作用域和动态作用域》](https://github.com/mqyqingfeng/Blog/issues/3)最后的问题：

```javascript
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f();
}
checkscope();
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f;
}
checkscope()();
```

两段代码执行的结果一样，但是两段代码究竟有哪些不同呢？

答案就是执行上下文栈的变化不一样。

让我们模拟第一段代码：

```javascript
ECStack.push(<checkscope> functionContext);
ECStack.push(<f> functionContext);
ECStack.pop();
ECStack.pop();
```

让我们模拟第二段代码：

```javascript
ECStack.push(<checkscope> functionContext);
ECStack.pop();
ECStack.push(<f> functionContext);
ECStack.pop();
```

### 2. 闭包

简单理解：https://zhuanlan.zhihu.com/p/56490498

1. js垃圾回收机制：js 中的变量和函数不再使用后，会被自动js垃圾回收机制回收。

2. 形成闭包的条件：有函数/作用域的嵌套；内部函数引用外部函数的变量/参数。

3. 闭包的结果：内部函数的使用外部函数的那些变量和参数仍然会保存，使用`return`返回了此内部函数，上面的变量和参数不会被回收。

4. 闭包的原因：返回的函数并非孤立的函数，而是连同周围的环境（AO）打了一个包，成了一个封闭的环境包，共同返回出来 ---->闭包。

5. 我们在返回函数的时候，并不是单纯的返回了一个函数，我们把该函数连同他的AO链一起返回了。

6. 函数的作用域，取决于声明时而不取决于调用时。

7. 变量存储`function(){}`、`{}`、`[]`存储的是一个地址。







##### 下面是 阮一峰的闭包解释：

**一、变量的作用域**

要理解闭包，首先必须理解Javascript特殊的变量作用域。

变量的作用域无非就是两种：全局变量和局部变量。

Javascript语言的特殊之处，就在于函数内部可以直接读取全局变量。

> 　　var n=999;
>
> 　　function f1(){
> 　　　　alert(n);
> 　　}
>
> 　　f1(); // 999

另一方面，在函数外部自然无法读取函数内的局部变量。

> 　　function f1(){
> 　　　　var n=999;
> 　　}
>
> 　　alert(n); // error

这里有一个地方需要注意，函数内部声明变量的时候，一定要使用var命令。如果不用的话，你实际上声明了一个全局变量！

> 　　function f1(){
> 　　　　n=999;
> 　　}
>
> 　　f1();
>
> 　　alert(n); // 999

**二、如何从外部读取局部变量？**

出于种种原因，我们有时候需要得到函数内的局部变量。但是，前面已经说过了，正常情况下，这是办不到的，只有通过变通方法才能实现。

那就是在函数的内部，再定义一个函数。

> 　　function f1(){
>
> 　　　　var n=999;
>
> 　　　　function f2(){
> 　　　　　　alert(n); // 999
> 　　　　}
>
> 　　}

在上面的代码中，函数f2就被包括在函数f1内部，这时f1内部的所有局部变量，对f2都是可见的。但是反过来就不行，f2内部的局部变量，对f1就是不可见的。这就是Javascript语言特有的"链式作用域"结构（chain scope），子对象会一级一级地向上寻找所有父对象的变量。所以，父对象的所有变量，对子对象都是可见的，反之则不成立。

既然f2可以读取f1中的局部变量，那么只要把f2作为返回值，我们不就可以在f1外部读取它的内部变量了吗！

> 　　function f1(){
>
> 　　　　var n=999;
>
> 　　　　function f2(){
> 　　　　　　alert(n);
> 　　　　}
>
> 　　　　return f2;
>
> 　　}
>
> 　　var result=f1();
>
> 　　result(); // 999

**三、闭包的概念**

上一节代码中的f2函数，就是闭包。

各种专业文献上的"闭包"（closure）定义非常抽象，很难看懂。我的理解是，闭包就是能够读取其他函数内部变量的函数。

由于在Javascript语言中，只有函数内部的子函数才能读取局部变量，因此可以把闭包简单理解成"定义在一个函数内部的函数"。

所以，在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。

**四、闭包的用途**

闭包可以用在许多地方。它的最大用处有两个，一个是前面提到的可以读取函数内部的变量，另一个就是让这些变量的值始终保持在内存中。

怎么来理解这句话呢？请看下面的代码。

> 　　function f1(){
>
> 　　　　var n=999;
>
> 　　　　nAdd=function(){n+=1}
>
> 　　　　function f2(){
> 　　　　　　alert(n);
> 　　　　}
>
> 　　　　return f2;
>
> 　　}
>
> 　　var result=f1();
>
> 　　result(); // 999
>
> 　　nAdd();
>
> 　　result(); // 1000

在这段代码中，result实际上就是闭包f2函数。它一共运行了两次，第一次的值是999，第二次的值是1000。这证明了，函数f1中的局部变量n一直保存在内存中，并没有在f1调用后被自动清除。

为什么会这样呢？原因就在于f1是f2的父函数，而f2被赋给了一个全局变量，这导致f2始终在内存中，而f2的存在依赖于f1，因此f1也始终在内存中，不会在调用结束后，被垃圾回收机制（garbage collection）回收。

这段代码中另一个值得注意的地方，就是"nAdd=function(){n+=1}"这一行，首先在nAdd前面没有使用var关键字，因此nAdd是一个全局变量，而不是局部变量。其次，nAdd的值是一个匿名函数（anonymous function），而这个匿名函数本身也是一个闭包，所以nAdd相当于是一个setter，可以在函数外部对函数内部的局部变量进行操作。

**五、使用闭包的注意点**

1）由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。

2）闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。



















##### 破解前端面试（80% 应聘者不及格系列）：从闭包说起

> 修订说明：发布《80% 应聘者都不及格的 JS 面试题》之后，全网阅读量超过 **6W**，在[知乎](https://zhuanlan.zhihu.com/p/25855075)、[掘金](https://juejin.im/post/6844903470466629640)、[cnodejs](http://cnodejs.org/topic/58cf2225df7ceac916b443e5) 都引发了很多讨论，还被多个前端微信公号和技术媒体转载。酝酿许久之后，笔者准备接下来撰写前端面试题系列文章，内容涵盖 DOM、HTTP、浏览器、框架、编码、工程化等方面，一方面给求职同学梳理面试关键点、理解前端知识脉络，另一方面也给面试官同行做参考，如何设计由浅入深的面试题。**本文是对第一篇文章的修订重发，读过的同学可以不用再读，感兴趣的可以看看追问 1里面的增补**，后续文章会在[知乎](https://zhuanlan.zhihu.com/feweekly)、[掘金](https://juejin.im/user/4072246758354680)、[cnodejs](http://cnodejs.org/user/wangshijun) 同步发布。
>
> 共 3599 字，读完需 7 分钟，速读需 3 分钟。写在前面，笔者在做面试官这 2 年多的时间内，面试了超过 200 个前端工程师，惊讶的发现，超过 80% 的候选人对下面这道跟闭包、定时器有关的面试题回答情况连及格都达不到。这究竟是怎样神奇的一道面试题？他考察了候选人的哪些能力？对正在读本文的你有什么启示？且听我慢慢道来。

***不起眼的开始***

招聘前端工程师，尤其是中高级前端工程师，扎实的 JS 基础绝对是必要条件，基础不扎实的工程师在面对前端开发中的各种问题时大概率会束手无策。在考察候选人 JS 基础的时候，我经常会提供下面这段代码，然后让候选人分析它实际运行的结果：

```javascript
for (var i = 0; i < 5; i++) {
    setTimeout(function() {
        console.log(new Date, i);
    }, 1000);
}

console.log(new Date, i);复制代码
```

这段代码很短，只有 7 行，我想，能读到这里的同学应该不需要我逐行解释这段代码在做什么吧。候选人面对这段代码时给出的结果也不尽相同，以下是典型的答案：

- A. 20% 的人会快速扫描代码，然后给出结果：`0,1,2,3,4,5`；
- B. 30% 的人会拿着代码逐行看，然后给出结果：`5,0,1,2,3,4`；
- C. 50% 的人会拿着代码仔细琢磨，然后给出结果：`5,5,5,5,5,5`；

只要你对 JS 中同步和异步代码的区别、变量作用域、闭包等概念有正确的理解，就知道正确答案是 C，代码的实际输出是：

```
2017-03-18T00:43:45.873Z 5
2017-03-18T00:43:46.866Z 5
2017-03-18T00:43:46.868Z 5
2017-03-18T00:43:46.868Z 5
2017-03-18T00:43:46.868Z 5
2017-03-18T00:43:46.868Z 5复制代码
```

接下来我会追问：如果我们约定，**用箭头表示其前后的两次输出之间有 1 秒的时间间隔，而逗号表示其前后的两次输出之间的时间间隔可以忽略**，代码实际运行的结果该如何描述？会有下面两种答案：

- A. 60% 的人会描述为：`5 -> 5 -> 5 -> 5 -> 5`，即每个 5 之间都有 1 秒的时间间隔；
- B. 40% 的人会描述为：`5 -> 5,5,5,5,5`，即第 1 个 5 直接输出，1 秒之后，输出 5 个 5；

这就要求候选人对 JS 中的[定时器工作机制](http://ejohn.org/blog/how-javascript-timers-work/)非常熟悉，循环执行过程中，几乎同时设置了 5 个定时器，一般情况下，这些定时器都会在 1 秒之后触发，而循环完的输出是立即执行的，显而易见，正确的描述是 B。

如果到这里算是及格的话，100 个人参加面试只有 20 人能及格，读到这里的同学可以仔细思考，你及格了么？

***追问 1：闭包***

如果这道题仅仅是考察候选人对 JS 异步代码、变量作用域的理解，局限性未免太大，接下来我会追问，如果期望代码的输出变成：`5 -> 0,1,2,3,4`，该怎么改造代码？熟悉闭包的同学很快能给出下面的解决办法：

```javascript
for (var i = 0; i < 5; i++) {
    (function(j) {  // j = i
        setTimeout(function() {
            console.log(new Date, j);
        }, 1000);
    })(i);
}

console.log(new Date, i);复制代码
```

巧妙的利用 [IIFE](http://benalman.com/news/2010/11/immediately-invoked-function-expression/#iife)（Immediately Invoked Function Expression：声明即执行的函数表达式）来解决闭包造成的问题，确实是不错的思路，但是初学者可能并不觉得这样的代码很好懂，至少笔者初入门的时候这里琢磨了一会儿才真正理解。

**增补**：如果有同学给出如下的解决方案，则说明他是一个仔细看[API 文档](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout)的人，这种习惯会让他学习的时候少走弯路，具体代码如下：

```javascript
for (var i = 0; i < 5; i++) {
    setTimeout(function(j) {
        console.log(new Date, j);
    }, 1000, i);
}

console.log(new Date, i);复制代码
```

有没有更符合直觉的做法？答案是有，我们只需要对循环体稍做手脚，让负责输出的那段代码能拿到每次循环的 `i` 值即可。该怎么做呢？利用 JS 中基本类型（Primitive Type）的参数传递是[按值传递](http://stackoverflow.com/questions/6605640/javascript-by-reference-vs-by-value)（Pass by Value）的特征，不难改造出下面的代码：

```
var output = function (i) {
    setTimeout(function() {
        console.log(new Date, i);
    }, 1000);
};

for (var i = 0; i < 5; i++) {
    output(i);  // 这里传过去的 i 值被复制了
}

console.log(new Date, i);复制代码
```

能给出上述 2 种解决方案的候选人可以认为对 JS 基础的理解和运用是不错的，可以各加 10 分。当然实际面试中还有候选人给出如下的代码：

```
for (let i = 0; i < 5; i++) {
    setTimeout(function() {
        console.log(new Date, i);
    }, 1000);
}

console.log(new Date, i);复制代码
```

细心的同学会发现，这里只有个非常细微的变动，即使用 ES6 [块级作用域](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Statements/let)（Block Scope）中的 `let` 替代了 `var`，但是代码在实际运行时会报错，因为最后那个输出使用的 `i` 在其所在的作用域中并不存在，`i` 只存在于循环内部。

能想到 ES6 特性的同学虽然没有答对，但是展示了自己对 ES6 的了解，可以加 5 分，继续进行下面的追问。

***追问 2：ES6***

有经验的前端同学读到这里可能有些不耐烦了，扯了这么多，都是他知道的内容，先别着急，挑战的难度会继续增加。

接着上文继续追问：如果期望代码的输出变成 `0 -> 1 -> 2 -> 3 -> 4 -> 5`，并且要求原有的代码块中的循环和两处 `console.log` 不变，该怎么改造代码？新的需求可以精确的描述为：代码执行时，立即输出 0，之后每隔 1 秒依次输出 `1,2,3,4`，循环结束后在大概第 5 秒的时候输出 5（这里使用**大概**，是为了避免钻牛角尖的同学陷进去，因为 JS 中的定时器触发时机有可能是不确定的，具体可参见 [How Javascript Timers Work](http://ejohn.org/blog/how-javascript-timers-work/)）。

看到这里，部分同学会给出下面的可行解：

```
for (var i = 0; i < 5; i++) {
    (function(j) {
        setTimeout(function() {
            console.log(new Date, j);
        }, 1000 * j);  // 这里修改 0~4 的定时器时间
    })(i);
}

setTimeout(function() { // 这里增加定时器，超时设置为 5 秒
    console.log(new Date, i);
}, 1000 * i);复制代码
```

不得不承认，这种做法虽粗暴有效，但是不算是能额外加分的方案。如果把这次的需求抽象为：在系列异步操作完成（每次循环都产生了 1 个异步操作）之后，再做其他的事情，代码该怎么组织？聪明的你是不是想起了什么？对，就是 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。

可能有的同学会问，不就是在控制台输出几个数字么？至于这样杀鸡用牛刀？你要知道，面试官真正想考察的是候选人是否具备某种能力和素质，因为在现代的前端开发中，处理异步的代码随处可见，熟悉和掌握异步操作的流程控制是成为合格开发者的基本功。

顺着下来，不难给出基于 Promise 的解决方案（既然 Promise 是 ES6 中的新特性，我们的新代码使用 ES6 编写是不是会更好？如果你这么写了，大概率会让面试官心生好感）：

```
const tasks = [];
for (var i = 0; i < 5; i++) {   // 这里 i 的声明不能改成 let，如果要改该怎么做？
    ((j) => {
        tasks.push(new Promise((resolve) => {
            setTimeout(() => {
                console.log(new Date, j);
                resolve();  // 这里一定要 resolve，否则代码不会按预期 work
            }, 1000 * j);   // 定时器的超时时间逐步增加
        }));
    })(i);
}

Promise.all(tasks).then(() => {
    setTimeout(() => {
        console.log(new Date, i);
    }, 1000);   // 注意这里只需要把超时设置为 1 秒
});复制代码
```

相比而言，笔者更倾向于下面这样看起来更简洁的代码，要知道编程风格也是很多面试官重点考察的点，代码阅读时的颗粒度更小，模块化更好，无疑会是加分点。

```
const tasks = []; // 这里存放异步操作的 Promise
const output = (i) => new Promise((resolve) => {
    setTimeout(() => {
        console.log(new Date, i);
        resolve();
    }, 1000 * i);
});

// 生成全部的异步操作
for (var i = 0; i < 5; i++) {
    tasks.push(output(i));
}

// 异步操作完成之后，输出最后的 i
Promise.all(tasks).then(() => {
    setTimeout(() => {
        console.log(new Date, i);
    }, 1000);
});复制代码
```

读到这里的同学，恭喜你，你下次面试遇到类似的问题，至少能拿到 80 分。

我们都知道使用 Promise 处理异步代码比回调机制让代码可读性更高，但是使用 Promise 的问题也很明显，即如果没有处理 Promise 的 reject，会导致错误被[丢进黑洞](http://blog.rangle.io/errors-in-promises/)，好在新版的 Chrome 和 Node 7.x 能对未处理的异常给出 [Unhandled Rejection Warning](https://nodejs.org/api/process.html#process_event_unhandledrejection)，而排查这些错误还需要一些特别的技巧（[浏览器](https://www.bennadel.com/blog/3239-logging-and-debugging-unhandled-promise-rejections-in-the-browser.htm)、[Node.js](https://www.bennadel.com/blog/3238-logging-and-debugging-unhandled-promise-rejections-in-node-js-v1-4-1-and-later.htm)）。





## 二 、 this/call/apply/bind

### 1.this

https://www.jianshu.com/p/d647aa6d1ae6

首先记住一个结论：

在一个函数上下文中，this由调用者提供，由调用函数的方式来决定。**如果调用者函数，被某一个对象所拥有，那么该函数在调用时，**

**内部的this指向该对象。如果函数独立调用，那么该函数内部的this，则指向undefined**。但是在非严格模式中，当this指向undefined时，

它会被自动指向全局对象。

##### **1.1  最常见的全局性调用**

从结论中我们可以看出，想要准确确定this指向，找到函数的调用者以及区分他是否是独立调用十分关键

```
1 function test(){
2 　　　　this.x = 1;
3 　　　　alert(this.x);
4 　　}
5 test(); // 1
```

  第五行这里，实际上是window.test();只不过省略了window。(这个我亲测过)因为，我们在function 或者 var 一个函数，变量时，实际上是在开辟window新的属性值，比如var a=1；实际上是widow.a=1;**因为调用者是window，所以this自然也是指向window了**



##### **1.2 对象内部函数自己调用**

```
1 var o = {
2     user:"追梦子",
3     fn:function(){
4         console.log(this.user);  //追梦子
5     }
6 }
7 o.fn();  //这个时候就是 对象是调用者
```



##### **1.3 多层对象调用**

 先看这个例子

```
var o = {
    user:"追梦子",
    fn:function(){
        console.log(this.user); //追梦子
    }
}
window.o.fn();
```

 看着好像是window调用了函数，但实际上this只想的却是o。为什么呢？在像这样的多层对象中，即使函数被最外层的对象间接调用，但this指向的还是直接调用它的上一级对象。看下面这个例子就好理解了

```
 1 var o = {
 2     a:10,
 3     b:{
 4         // a:12,
 5         fn:function(){
 6             console.log(this.a); //undefined
 7         }
 8     }
 9 }
10 o.b.fn();
```

**直接调用fn()的仍然是b，不管怎样，我就只看真正直接调用者是谁**





##### **1.4  不妨看看下面这个和上面有什么不同**

```
 1 var o = {
 2     a:10,
 3     b:{
 4         a:12,
 5         fn:function(){
 6             console.log(this.a); //undefined
 7             console.log(this); //window
 8         }
 9     }
10 }
11 var j = o.b.fn;
12 j();
```

   是的，我们把函数保存进一个变量j里面了，但我们前面说过啊，这种var实际上是开辟了window的属性，所以j是属于window对象的 ，真正调用的是j这个函数，那明显this也就只想它所属的对象window了。





### 2.call

下面是一个简单的例子

```
var person = {
  name: "axuebin",
  age: 25
};
function say(job){
  console.log(this.name+":"+this.age+" "+job);
}
say.call(person,"FE"); // axuebin:25 FE
say.apply(person,["FE"]); // axuebin:25 FE
var sayPerson = say.bind(person,"FE");
sayPerson(); // axuebin:25 FE
```

它的语法是这样的：

```
fun.call(thisArg[,arg1[,arg2,…]]);
```

其中，`thisArg`就是`this`指向，`arg`是指定的参数。

`call`的用处简而言之就是可以让call()中的对象调用当前对象所拥有的function。



### 3. apply

它的语法是这样的：

```
fun.apply(thisArg, [argsArray]);
```

其中，`thisArg`就是`this`指向，`argsArray`是指定的参数数组。

通过语法就可以看出`call`和`apply`的在参数上的一个区别：

- `call`的参数是一个列表，将每个参数一个个列出来
- `apply`的参数是一个数组，将每个参数放到一个数组中

***用法***

在用法上`apply`和`call`一样，就不说了。

### 4.bind

语法：

```
fun.bind(thisArg[, arg1[, arg2[, ...]]])
```

其中，`thisArg`就是`this`指向，`arg`是指定的参数。

可以看出，`bind`会创建一个新函数（称之为绑定函数），原函数的一个拷贝，也就是说不会像`call`和`apply`那样立即执行。

当这个绑定函数被调用时，它的`this`值传递给`bind`的一个参数，执行的参数是传入`bind`的其它参数和执行绑定函数时传入的参数。

**用法**

当我们执行下面的代码时，我们希望可以正确地输出`name`，然后现实是残酷的

```
function Person(name){
  this.name = name;
  this.say = function(){
    setTimeout(function(){
      console.log("hello " + this.name);
    },1000)
  }
}
var person = new Person("axuebin");
person.say(); //hello undefined
```

###  5. call/apply 的原理模拟实现

##### call

一句话介绍 call：

> call() 方法在使用一个指定的 this 值和若干个指定的参数值的前提下调用某个函数或方法。

举个例子：

```
var foo = {
    value: 1
};

function bar() {
    console.log(this.value);
}

bar.call(foo); // 1复制代码
```

注意两点：

1. call 改变了 this 的指向，指向到 foo
2. bar 函数执行了

##### 模拟实现第一步

那么我们该怎么模拟实现这两个效果呢？

试想当调用 call 的时候，把 foo 对象改造成如下：

```
var foo = {
    value: 1,
    bar: function() {
        console.log(this.value)
    }
};

foo.bar(); // 1复制代码
```

这个时候 this 就指向了 foo，是不是很简单呢？

但是这样却给 foo 对象本身添加了一个属性，这可不行呐！

不过也不用担心，我们用 delete 再删除它不就好了~

所以我们模拟的步骤可以分为：

1. 将函数设为对象的属性
2. 执行该函数
3. 删除该函数

以上个例子为例，就是：

```
// 第一步
foo.fn = bar
// 第二步
foo.fn()
// 第三步
delete foo.fn复制代码
```

fn 是对象的属性名，反正最后也要删除它，所以起成什么都无所谓。

根据这个思路，我们可以尝试着去写第一版的 **call2** 函数：

```
// 第一版
Function.prototype.call2 = function(context) {
    // 首先要获取调用call的函数，用this可以获取
    //下面这个代码就是给 对象添加一个 fn属性，这属性就是函数
    context.fn = this;
    context.fn();
    delete context.fn;
}

// 测试一下
var foo = {
    value: 1
};

function bar() {
    console.log(this.value);
}

bar.call2(foo); // 1复制代码
```

正好可以打印 1 哎！是不是很开心！(～￣▽￣)～

##### 模拟实现第二步

最一开始也讲了，call 函数还能给定参数执行函数。举个例子：

```
var foo = {
    value: 1
};

function bar(name, age) {
    console.log(name)
    console.log(age)
    console.log(this.value);
}

bar.call(foo, 'kevin', 18);
// kevin
// 18
// 1复制代码
```

注意：传入的参数并不确定，这可咋办？

不急，我们可以从 Arguments 对象中取值，取出第二个到最后一个参数，然后放到一个数组里。

比如这样：

```
// 以上个例子为例，此时的arguments为：
// arguments = {
//      0: foo,
//      1: 'kevin',
//      2: 18,
//      length: 3
// }
// 因为arguments是类数组对象，所以可以用for循环
var args = [];
for(var i = 1, len = arguments.length; i < len; i++) {
    args.push('arguments[' + i + ']');
}

// 执行后 args为 [foo, 'kevin', 18]复制代码
```

不定长的参数问题解决了，我们接着要把这个参数数组放到要执行的函数的参数里面去。

```
// 将数组里的元素作为多个参数放进函数的形参里
context.fn(args.join(','))
// (O_o)??
// 这个方法肯定是不行的啦！！！复制代码
```

也许有人想到用 ES6 的方法，不过 call 是 ES3 的方法，我们为了模拟实现一个 ES3 的方法，要用到ES6的方法，好像……，嗯，也可以啦。但是我们这次用 eval 方法拼成一个函数，类似于这样：

```
eval('context.fn(' + args +')')复制代码
```

这里 args 会自动调用 Array.toString() 这个方法。

所以我们的第二版克服了两个大问题，代码如下：

```
// 第二版
Function.prototype.call2 = function(context) {
    context.fn = this;
    var args = [];
    for(var i = 1, len = arguments.length; i < len; i++) {
        args.push('arguments[' + i + ']');
    }
    eval('context.fn(' + args +')');
    delete context.fn;
}

// 测试一下
var foo = {
    value: 1
};

function bar(name, age) {
    console.log(name)
    console.log(age)
    console.log(this.value);
}

bar.call2(foo, 'kevin', 18); 
// kevin
// 18
// 1复制代码
```



##### 模拟实现第三步

模拟代码已经完成 80%，还有两个小点要注意：

**1.this 参数可以传 null，当为 null 的时候，视为指向 window**

举个例子：

```
var value = 1;

function bar() {
    console.log(this.value);
}

bar.call(null); // 1复制代码
```

虽然这个例子本身不使用 call，结果依然一样。

**2.函数是可以有返回值的！**

举个例子：

```
var obj = {
    value: 1
}

function bar(name, age) {
    return {
        value: this.value,
        name: name,
        age: age
    }
}

console.log(bar.call(obj, 'kevin', 18));
// Object {
//    value: 1,
//    name: 'kevin',
//    age: 18
// }复制代码
```

不过都很好解决，让我们直接看第三版也就是最后一版的代码：

```
// 第三版
Function.prototype.call2 = function (context) {
    var context = context || window;
    context.fn = this;

    var args = [];
    for(var i = 1, len = arguments.length; i < len; i++) {
        args.push('arguments[' + i + ']');
    }

    var result = eval('context.fn(' + args +')');

    delete context.fn
    return result;
}

// 测试一下
var value = 2;

var obj = {
    value: 1
}

function bar(name, age) {
    console.log(this.value);
    return {
        value: this.value,
        name: name,
        age: age
    }
}

bar.call(null); // 2

console.log(bar.call2(obj, 'kevin', 18));
// 1
// Object {
//    value: 1,
//    name: 'kevin',
//    age: 18
// }复制代码
```

到此，我们完成了 call 的模拟实现，给自己一个赞 ｂ（￣▽￣）ｄ

##### apply的模拟实现

apply 的实现跟 call 类似，在这里直接给代码，代码来自于知乎 @郑航的实现：

```
Function.prototype.apply = function (context, arr) {
    var context = Object(context) || window;
    context.fn = this;

    var result;
    if (!arr) {
        result = context.fn();
    }
    else {
        var args = [];
        for (var i = 0, len = arr.length; i < len; i++) {
            args.push('arr[' + i + ']');
        }
        result = eval('context.fn(' + args + ')')
    }

    delete context.fn
    return result;
}
```

作者：冴羽
链接：https://juejin.cn/post/6844903476477034510
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。





### 6.bind的原理模拟实现









## 三、原型/继承





### 1.原型

**这部分先参考之前的 notebook 记录的关于原型的笔记**





那原型对象是用来做什么的呢？主要作用是用于继承。举个例子：



```javascript
  var Person = function(name){
    this.name = name; // tip: 当函数执行时这个 this 指的是谁？
  };
  Person.prototype.getName = function(){
    return this.name;  // tip: 当函数执行时这个 this 指的是谁？
  }
  var person1 = new person('Mick');
  person1.getName(); //Mick
```

从这个例子可以看出，通过给 `Person.prototype` 设置了一个函数对象的属性，那有 Person 的实例（person1）出来的普通对象就继承了这个属性。具体是怎么实现的继承，就要讲到下面的原型链了。





### 2. 各种原型模式的优缺点

```
function Person(name) {

}

Person.prototype.name = 'keivn';
Person.prototype.getName = function () {
    console.log(this.name);
};

var person1 = new Person();
```

优点：方法不会重新创建

缺点：1. 所有的属性和方法都共享 2. 不能初始化参数

#### 2.2.1 原型模式优化

```
function Person(name) {

}

Person.prototype = {
    name: 'kevin',
    getName: function () {
        console.log(this.name);
    }
};

var person1 = new Person();
```

优点：封装性好了一点

缺点：重写了原型，丢失了constructor属性

#### 2.2.2 原型模式优化

```
function Person(name) {

}

Person.prototype = {
   //重新使这个constructor 指向原来的 Person 对象
    constructor: Person,
    name: 'kevin',
    getName: function () {
        console.log(this.name);
    }
};

var person1 = new Person();
```

优点：实例可以通过constructor属性找到所属构造函数

缺点：原型模式该有的缺点还是有

#### 2.2.3 组合模式

构造函数模式与原型模式双剑合璧。

```
function Person(name) {
    this.name = name;
}

Person.prototype = {
    constructor: Person,
    getName: function () {
        console.log(this.name);
    }
};

var person1 = new Person();
```

**优点：该共享的共享，该私有的私有，使用最广泛的方式**

缺点：有的人就是希望全部都写在一起，即更好的封装性

其中组合模式 确实是使用最广的









## 四、 Promise

### 1.异步请求简介

***为什么要有同步异步？***

首先JS是一个单线程的语言。单线程的含义类似于从头走到尾，谁也别管谁，前面堵车我就停（官方：单线程在程序执行时，所走的程序路径按照连续顺序排下来，前面 的必须处理好，后面的才会执行。），没办法开多个线程。

疑问来了？为什么JS不设计成多线程的可以开多个线程同时操作。JS是可以去操作DOM的。假设JS设计成一个多线程语言。你的主线程在给DOM的innerHtml做一个赋值操作，你的另一个线程把这个DOM结构删除了。。。。这肯定不可以。（多线程可以互不干预的操作一段内存空间）。所以干脆设计成一个单线程，哪怕后期HTML5出现的web worker也是不允许操作DOM结构的，可以完成一些分布式的计算。对于dom结构我们必须顺序操纵，坚决不允许出现对同一个dom同时进行操作。

但是浏览器加载一些需要网络请求的比如图片资源、ajax。或者轮训的内容。由于是单线程，需要等待这些内容访问完才可以执行下面的代码。那么你发个ajax请求或者请求个图片资源，那么这段时间就什么也做不了，这种效果对程序是一种阻塞，在等待时间明明可以做些别的事情却选择了无意义的等待。（同步阻塞）这个时候异步就出现了，在涉及需要等待的操作，我们选择让程序继续运行，在等待时间结束的时候，通知一下我们的程序内容执行完毕，你可以操作这些资源了，这段等待时间并不影响你程序的继续执行，只是在未来的某个时间段（不确定），有一个操作一定会执行，这就是异步。（异步非阻塞），这就是为什么要有同步异步。

***基础的内容我们讲解完毕，下面开始真正的干货***

> （第一次了解这个的，请先补习一下定时器与promise的知识）

```
xxxxxxxxx(一堆不确定的代码)

setTimeout(()=>{console.log(111)},500);
复制代码
```

请问打印出111会在什么时候？ 答案是500ms的时候打印

错！答案是500ms或者500ms以后的某个时间段。

首先遇见定时器后，会将定时器内的函数进行注册，也就是放入Event Table 。然后在500ms后将Event Table内注册的函数放入 Event queue。若主线程（我也就一个线程）中的call stack（调用堆栈，也就是线程中函数的一个调用栈）为空就将Event queue按顺序的放入call stack中进行执行。如果call stack并不为空， Event queue内的事件并不会进入call stack中，也就不会执行。

怎么突然来了这么多乱七八糟，画风和刚才不一样呀。难度上来了。

首先对于异步事件，我们在执行到这行代码的时候会进行一个注册，将你要在未来某个时间段要执行的函数注册一下，放在Event table中。这个Event table中可以有很多事件，比如你一次发了好多ajax请求，那么他们就全部注册了。在未来的时间到了，就会把注册的事件放入Event queue（任务队列）这个任务队列就是马上要执行的内容。

任务队列什么时候可以执行？在主线程的call stack为空的时候，会把任务队列的第一个事件放入call stack中执行，这里面涉及一个queue（队列）的特点就是先进先出。在注册后先放入Event queue的事件就会更早的离开Event queue进入主线程执行。这个时候是不是觉得自己明白点了？ 唉别高兴的太早了。

***来吧 聊一聊宏任务与微任务吧***

```
setTimeout(()=>{console.log(111)},0)

let promise = new Promise((resolve,reject)=>{
	console.log(222);
	resolve(3333)
});

let promise2 = new Promise((resolve,reject)=>{
	console.log(555);
	resolve(6666)
});

setTimeout(()=>{
	console.log(4444);
},0)

promise.then(res=>{
	console.log(res);
});

promise2.then(res=>{
	console.log(res);
});
复制代码
```

你说这会打印出个什么？ 按理论先进先出，222 555 111 4444 333 666 你看对不对。 事实证明：222 555 333 666 111 4444 这是为什么？ 首先注册的事件也有不同形态，宏任务与微任务。

常见的宏任务：setTimeout、setInterval（定时器类）

常见的微任务：promise process.nextTick(一个node环境的方法)。

这两个任务的执行规则是什么？首先在call stack中的内容执行完毕清空后，会在Event queue检查一下哪些是宏任务哪些是微任务，然后执行所有的微任务，然后执行一个宏任务，之后再次执行所有的微任务。也就是说在主线程任务执行完毕后会把任务队列中的微任务全部执行，然后再执行一个宏任务，这个宏任务执行完再次检查队列内部的微任务，有就全部执行没有就再执行一个宏任务。

## 五、JavaScript基础心法——深浅拷贝 

浅拷贝和深拷贝都是对于JS中的引用类型而言的，浅拷贝就只是复制对象的引用，如果拷贝后的对象发生变化，原对象也会发生变化。只有深拷贝才是真正地对对象的拷贝。

------

**前言**

说到深浅拷贝，必须先提到的是JavaScript的数据类型，之前的一篇文章[JavaScript基础心法——数据类型](https://github.com/axuebin/articles/issues/3)说的很清楚了，这里就不多说了。

需要知道的就是一点：JavaScript的数据类型分为基本数据类型和引用数据类型。

**对于基本数据类型的拷贝，并没有深浅拷贝的区别，**（一定要记住这个 ，基本数据类型就算拷贝了，两个 就不会有任何关系了）我们所说的深浅拷贝都是对于引用数据类型而言的。

### 浅拷贝

浅拷贝的意思就是只复制引用，而未复制真正的值。

```javascript
const originArray = [1,2,3,4,5];
const originObj = {a:'a',b:'b',c:[1,2,3],d:{dd:'dd'}};

const cloneArray = originArray;
const cloneObj = originObj;

console.log(cloneArray); // [1,2,3,4,5]
console.log(originObj); // {a:'a',b:'b',c:Array[3],d:{dd:'dd'}}

cloneArray.push(6);
cloneObj.a = {aa:'aa'};

console.log(cloneArray); // [1,2,3,4,5,6]
console.log(originArray); // [1,2,3,4,5,6]

console.log(cloneObj); // {a:{aa:'aa'},b:'b',c:Array[3],d:{dd:'dd'}}
console.log(originArray); // {a:{aa:'aa'},b:'b',c:Array[3],d:{dd:'dd'}}
```

上面的代码是最简单的利用 `=` 赋值操作符实现了一个浅拷贝，可以很清楚的看到，随着 `cloneArray` 和 `cloneObj` 改变，`originArray` 和 `originObj` 也随着发生了变化。

### 深拷贝

深拷贝就是对目标的完全拷贝，不像浅拷贝那样只是复制了一层引用，就连值也都复制了。

只要进行了深拷贝，它们老死不相往来，谁也不会影响谁。

目前实现深拷贝的方法不多，主要是两种：

1. 利用 `JSON` 对象中的 `parse` 和 `stringify`
2. 利用递归来实现每一层都重新创建对象并赋值

#### JSON.stringify/parse的方法

先看看这两个方法吧：

> The JSON.stringify() method converts a JavaScript value to a JSON string.

`JSON.stringify` 是将一个 `JavaScript` 值转成一个 `JSON` 字符串。

> The JSON.parse() method parses a JSON string, constructing the JavaScript value or object described by the string.

`JSON.parse` 是将一个 `JSON` 字符串转成一个 `JavaScript` 值或对象。

很好理解吧，就是 `JavaScript` 值和 `JSON` 字符串的相互转换。

它能实现深拷贝呢？我们来试试。

```javascript
const originArray = [1,2,3,4,5];
const cloneArray = JSON.parse(JSON.stringify(originArray));
console.log(cloneArray === originArray); // false

const originObj = {a:'a',b:'b',c:[1,2,3],d:{dd:'dd'}};
const cloneObj = JSON.parse(JSON.stringify(originObj));
console.log(cloneObj === originObj); // false

cloneObj.a = 'aa';
cloneObj.c = [1,1,1];
cloneObj.d.dd = 'doubled';

console.log(cloneObj); // {a:'aa',b:'b',c:[1,1,1],d:{dd:'doubled'}};
console.log(originObj); // {a:'a',b:'b',c:[1,2,3],d:{dd:'dd'}};
```

确实是深拷贝，也很方便。但是，这个方法只能适用于一些简单的情况。比如下面这样的一个对象就不适用：

```javascript
const originObj = {
  name:'axuebin',
  sayHello:function(){
    console.log('Hello World');
  }
}
console.log(originObj); // {name: "axuebin", sayHello: ƒ}
const cloneObj = JSON.parse(JSON.stringify(originObj));
console.log(cloneObj); // {name: "axuebin"}
```

发现在 `cloneObj` 中，有属性丢失了。。。那是为什么呢？

在 `MDN` 上找到了原因：

> If undefined, a function, or a symbol is encountered during conversion it is either omitted (when it is found in an object) or censored to null (when it is found in an array). JSON.stringify can also just return undefined when passing in "pure" values like JSON.stringify(function(){}) or JSON.stringify(undefined).

`undefined`、`function`、`symbol` 会在转换过程中被忽略。。。

明白了吧，就是说如果对象中含有一个函数时（很常见），就不能用这个方法进行深拷贝。

#### 递归的方法

递归的思想就很简单了，就是对每一层的数据都实现一次 `创建对象->对象赋值` 的操作，简单粗暴上代码：

```javascript
function deepClone(source){
  const targetObj = source.constructor === Array ? [] : {}; // 判断复制的目标是数组还是对象
  for(let keys in source){ // 遍历目标
    if(source.hasOwnProperty(keys)){
      if(source[keys] && typeof source[keys] === 'object'){ // 如果值是对象，就递归一下
        targetObj[keys] = source[keys].constructor === Array ? [] : {};
        targetObj[keys] = deepClone(source[keys]);
      }else{ // 如果不是，就直接赋值
        targetObj[keys] = source[keys];
      }
    } 
  }
  return targetObj;
}
```

我们来试试：

```javascript
const originObj = {a:'a',b:'b',c:[1,2,3],d:{dd:'dd'}};
const cloneObj = deepClone(originObj);
console.log(cloneObj === originObj); // false

cloneObj.a = 'aa';
cloneObj.c = [1,1,1];
cloneObj.d.dd = 'doubled';

console.log(cloneObj); // {a:'aa',b:'b',c:[1,1,1],d:{dd:'doubled'}};
console.log(originObj); // {a:'a',b:'b',c:[1,2,3],d:{dd:'dd'}};
```

可以。那再试试带有函数的：

```javascript
const originObj = {
  name:'axuebin',
  sayHello:function(){
    console.log('Hello World');
  }
}
console.log(originObj); // {name: "axuebin", sayHello: ƒ}
const cloneObj = deepClone(originObj);
console.log(cloneObj); // {name: "axuebin", sayHello: ƒ}
```

也可以。搞定。

是不是以为这样就完了？？ 当然不是。

#### JavaScript中的拷贝方法

我们知道在 `JavaScript` 中，数组有两个方法 `concat` 和 `slice` 是可以实现对原数组的拷贝的，这两个方法都不会修改原数组，而是返回一个修改后的新数组。

同时，ES6 中 引入了 `Object.assgn` 方法和 `...` 展开运算符也能实现对对象的拷贝。

那它们是浅拷贝还是深拷贝呢？

#### concat

> The concat() method is used to merge two or more arrays. This method does not change the existing arrays, but instead returns a new array.

该方法可以连接两个或者更多的数组，但是它不会修改已存在的数组，而是返回一个新数组。

看着这意思，很像是深拷贝啊，我们来试试：

```javascript
const originArray = [1,2,3,4,5];
const cloneArray = originArray.concat();

console.log(cloneArray === originArray); // false
cloneArray.push(6); // [1,2,3,4,5,6]
console.log(originArray); [1,2,3,4,5];
```

看上去是深拷贝的。

我们来考虑一个问题，如果这个对象是多层的，会怎样。

```javascript
const originArray = [1,[1,2,3],{a:1}];
const cloneArray = originArray.concat();
console.log(cloneArray === originArray); // false
cloneArray[1].push(4);
cloneArray[2].a = 2; 
console.log(originArray); // [1,[1,2,3,4],{a:2}]
```

`originArray` 中含有数组 `[1,2,3]` 和对象 `{a:1}`，如果我们直接修改数组和对象，不会影响 `originArray`，但是我们修改数组 `[1,2,3]` 或对象 `{a:1}` 时，发现 `originArray` 也发生了变化。

**结论：`concat` 只是对数组的第一层进行深拷贝。**

#### slice

> The slice() method returns a shallow copy of a portion of an array into a new array object selected from begin to end (end not included). The original array will not be modified.

解释中都直接写道是 `a shallow copy` 了 ~

但是，并不是！

```javascript
const originArray = [1,2,3,4,5];
const cloneArray = originArray.slice();

console.log(cloneArray === originArray); // false
cloneArray.push(6); // [1,2,3,4,5,6]
console.log(originArray); [1,2,3,4,5];
```

同样地，我们试试多层的数组。

```javascript
const originArray = [1,[1,2,3],{a:1}];
const cloneArray = originArray.slice();
console.log(cloneArray === originArray); // false
cloneArray[1].push(4);
cloneArray[2].a = 2; 
console.log(originArray); // [1,[1,2,3,4],{a:2}]
```

果然，结果和 `concat` 是一样的。

**结论：`slice` 只是对数组的第一层进行深拷贝。**

#### Object.assign()

> The Object.assign() method is used to copy the values of all enumerable own properties from one or more source objects to a target object. It will return the target object.

复制复制复制。

那到底是浅拷贝还是深拷贝呢？

自己试试吧。。

**结论：`Object.assign()` 拷贝的是属性值。假如源对象的属性值是一个指向对象的引用，它也只拷贝那个引用值。**

#### ... 展开运算符

```javascript
const originArray = [1,2,3,4,5,[6,7,8]];
const originObj = {a:1,b:{bb:1}};

const cloneArray = [...originArray];
cloneArray[0] = 0;
cloneArray[5].push(9);
console.log(originArray); // [1,2,3,4,5,[6,7,8,9]]

const cloneObj = {...originObj};
cloneObj.a = 2;
cloneObj.b.bb = 2;
console.log(originObj); // {a:1,b:{bb:2}}
```

**结论：`...` 实现的是对象第一层的深拷贝。后面的只是拷贝的引用值。**

#### 首层浅拷贝

我们知道了，会有一种情况，就是对目标对象的第一层进行深拷贝，然后后面的是浅拷贝，可以称作“首层浅拷贝”。

我们可以自己实现一个这样的函数：

```javascript
function shallowClone(source) {
  const targetObj = source.constructor === Array ? [] : {}; // 判断复制的目标是数组还是对象
  for (let keys in source) { // 遍历目标
    if (source.hasOwnProperty(keys)) {
      targetObj[keys] = source[keys];
    }
  }
  return targetObj;
}
```

我们来测试一下：

```javascript
const originObj = {a:'a',b:'b',c:[1,2,3],d:{dd:'dd'}};
const cloneObj = shallowClone(originObj);
console.log(cloneObj === originObj); // false
cloneObj.a='aa';
cloneObj.c=[1,1,1];
cloneObj.d.dd='surprise';
```

经过上面的修改，`cloneObj` 不用说，肯定是 `{a:'aa',b:'b',c:[1,1,1],d:{dd:'surprise'}}` 了，那 `originObj` 呢？刚刚我们验证了 `cloneObj === originObj` 是 `false`，说明这两个对象引用地址不同啊，那应该就是修改了 `cloneObj` 并不影响 `originObj`。

```javascript
console.log(cloneObj); // {a:'aa',b:'b',c:[1,1,1],d:{dd:'surprise'}}
console.log(originObj); // {a:'a',b:'b',c:[1,2,3],d:{dd:'surprise'}}
```

What happend?

`originObj` 中关于 `a`、`c`都没被影响，但是 `d` 中的一个对象被修改了。。。说好的深拷贝呢？不是引用地址都不一样了吗？

原来是这样：

1. 从 `shallowClone` 的代码中我们可以看出，我们只对第一层的目标进行了 `深拷贝` ，而第二层开始的目标我们是直接利用 `=` 赋值操作符进行拷贝的。
2. so，第二层后的目标都只是复制了一个引用，也就是浅拷贝。

#### 总结

1. 赋值运算符 `=` 实现的是浅拷贝，只拷贝对象的引用值；
2. JavaScript 中数组和对象自带的拷贝方法都是“首层浅拷贝”；
3. `JSON.stringify` 实现的是深拷贝，但是对目标对象有要求；
4. 若想真正意义上的深拷贝，请递归。
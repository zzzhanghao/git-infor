





**[VUE——一个MVVM（MODEL / VIEW / VIEWMODEL）的前端框架](https://www.cnblogs.com/xtxt1127/p/11736558.html)**





## 一、Vue   MVVM

MVVM模式：M（model）+ V（view）+ VM（viewmodel）与 MVC模式：M（model）、V（view）、C（controller）相类似

**MVC：**

![img](https://img-blog.csdnimg.cn/2020081217550388.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0tFWV9jaGV1bmc=,size_16,color_FFFFFF,t_70)

用户向controller层发起请求，controller收到请求后交给model中处理再将结果返回到controller中，controller收到结果后对view进行相对应的页面渲染再反馈给用户。所有操作都在controller中进行，当用户发起大量的请求后，controller加载速度变慢，视图渲染性能降低，进而影响用户体验。

**MVVM：**

![img](https://img-blog.csdnimg.cn/20200812174310572.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0tFWV9jaGV1bmc=,size_16,color_FFFFFF,t_70)

​	



**首先先介绍下几种常见的模式**：参考文章 https://juejin.cn/post/6856590487504191501

**最简单的例子**

用一个最简单的例子来展示各种设计模式

页面有一个 id 为 container 的 span，点击按钮会让其内容加 1

```javascript
<div>
    <span id="container">0</span>
  <button id="btn" onclick="javascript:add()">+</button>
</div>

<script>
  function add (){
    const container = document.getElementById('container');
    const current = parseInt(container.innerText);
    container.innerText = current + 1;
  }
</script>
```

视图渲染和数据处理的逻辑杂糅在一起，随着业务逻辑变复杂，代码将失控，难以维护

#### **MVC**

MVC 是 Model View Controller 的缩写

- Model：模型层，数据相关的操作
- View：视图层，用户界面渲染逻辑
- Controller：控制器，数据模型和视图之间通信的桥梁

MVC 模型有很多变种和数据流动方式，最传统的 MVC 模型把视图渲染和数据处理做了隔离，通过控制器接收 View 操作，传递给数据模型，数据 ready 后由数据模型驱动视图渲染

上面的例子用 MVC 模式来写可以做简单的代码分离

view

```javascript
<div>
    <span id="container">0</span>
  <button id="btn">+</button>
</div>

```

model

这个model的代码 最后还是要通过innerText的方法去影响view 层，所以跟view层还是没有完全的剥离联系

```javascript
function add (node) {
  // 业务逻辑处理
  const currentValue = parseInt(node.innerText);
  const newValue = currentValue + 1;

  // 更新视图
    node.innerText = current + 1;
}

```

controller

```javascript
const button = document.getElementById('btn');
// 响应视图指令
button.addEventListener('click', () => {
  const container = document.getElementById('container');

  // 调用模型
    add(container);
}, false);

```

1. 视图层最简单，处理页面的渲染
2. 模型层定义了 +1 操作的实现，并更新视图数据
3. 控制器在用户点击按钮的时候把请求转发给模型处理，在 web 开发中一般页面、接口请求的路由也是控制器负责。

在上面例子中为了尽量让数据处理和 UI 隔离，Controller 获取了 container 节点，做为参数传给了 Model，这样 Controller 需要理解 View，也就是和 View 的实现还是存在耦合，在 MVC 的实践中相当程度的业务逻辑实际会被写在 Controller 中，因为 Controller 被定位为 View 的 Model 沟通的桥梁，这部分耦合可以接受

但因为 View 的更新由 Model 处理，所以 Model 难免要和 View 的实现耦合，可以使用观察者模式让 View 监听 Mode 的数据变化做出更新，但这样 View 的实现又依赖的 Model

#### MVP

MVP 是 Model View Presenter 的缩写，可以说是 MVC 模式的改良，相对于 MVC 有了各层负责的任务和数据流动方式都有了部分变化

- Model：和具体业务无关的数据处理
- View：用户界面渲染逻辑
- Presenter：响应视图指令，同时进行相关业务处理，必要时候获调用 Model 获取底层数据，返回指令结果到视图，驱动视图渲染

MVP 模式相对于 MVC 有几个核心变化

1. View 和 Model 完全隔离，Model 不再负责业务逻辑和视图变化，只负责底层数据处理
2. Presenter 接管路由和业务逻辑，但要求 View 实现 View Interface，方便和具体 View 解耦，可以不依赖 UI 进行单元测试
3. View 层只负责发起指令和根据数据渲染 UI，不再有主动监听数据变化等行为，所以也被称之为被动视图

使用 MVP 模式修改上面例子

view

```javascript
<div>
    <span id="container">0</span>
  <button id="btn">+</button>
</div>
<script>
  // View Interface
    const globalConfig = {
    containerId: 'container',
    buttonId: 'btn',
  };
</script>

```

model

从下面这个model层的代码可以明显的看到，方法本身就跟view层完全剥离了，只实现特定的功能

```javascript
function add (num) {
  return num + 1;
}

```

presenter

```javascript
const button = document.getElementById(globalConfig.containerId);
const container = document.getElementById(globalConfig.buttonId);

// 响应视图指令
button.addEventListener('click', () => {
  const currentValue = parseInt(container.innerText);
  // 调用模型
    const newValue = add(currentValue);
  // 更新视图
  container.innerText = current + 1;
}, false);

```

这样 Model 只处理业务无关的数据处理，会变得非常稳定，同时 Presenter 和 View 通过接口/配置桥接，相对于直接 MVC 耦合降低了很多

可以看出 MVP 相对于 MVC 数据与视图分离做的更为出色，在大部分时候使用 MVC 其实是在使用 MVP

#### MVVM

MVVM 可以写成 MV-VM，是 Model View - ViewModel 的缩写，可以算是 MVP 模式的变种，View 和 Model 职责和 MVP 相同，但 ViewModel 主要靠 DataBinding 把 View 和 Model 做了自动关联，框架替应用开发者实现数据变化后的视图更新，相当于简化了 Presenter 的部分功能



前端比较熟悉的 Vue 正是使用 MVVM 模式，使用 Vue 实现示例功能

> 示例功能用 Vue 可以轻松实现，为了展示 MVVM 示例代码刻意兜了一个圈子

view

```javascript
<div id="test">
  <!-- 数据和视图绑定 -->
    <span>{{counter}}</span>
  <button v-on:click="counterPlus">+</button>
</div>
复制代码
```

model

```javascript
function add (num) {
  return num + 1;
}
复制代码
```

viewmodel

至于这个 viewmodel的实现看下面的解析

```javascript
new Vue({
  el: '#test',
  data: {
    counter: 0
  },
  methods: {
    counterPlus: function () {
        // 只需要修改数据，无需手工修改视图
        this.counter = add(this.counter);
    }
  }
})
复制代码
```

在 View 中做了数据和视图的绑定，在 ViewModel 中只需要更新数据，视图就会自动变化，DataBinding 由框架实现

**总结**

MVC、MVP、MVVM 三种流行的设计模式主要都是在解决数据和视图逻辑的分离问题，在实际使用中还有很多变种，总体而言

- MVC 对视图和数据做了第一步的分离，实现简单，但 View、业务逻辑、底层数据模型 分离的不彻底
- MVP 通过 Presenter 彻底解耦了 View 和 Model，同时剥离了业务逻辑和底层数据逻辑，让 Model 变得稳定，但业务逻辑复杂情况下 Presenter 会相对臃肿
- MVVM 通过 DataBinding 实现了视图和数据的绑定，但依赖框架实现，增加了理解成本，在错误使用的情况下调试复杂

设计模式没有绝对的优劣之分，具体选择要根据项目的规模和团队协同情况而定，后面介绍的 egg.js 虽然目录结构中使用 Controller，但实际是 MVP 模式





#### **MVVM 原理实现**



**MVVM 实现原理参考**： https://juejin.cn/post/6844903586103558158#heading-5

MVVM 双向数据绑定 在Angular1.x版本的时候通过的是**脏值检测**来处理

而现在无论是React还是Vue还是最新的Angular，其实实现方式都更相近了

那就是通过“**数据劫持+发布订阅模式**“ 划重点！！ 

真正实现其实靠的也是ES5中提供的**Object.defineProperty**，当然这是不兼容的所以Vue等只支持了IE8+

**为什么是它**

Object.defineProperty()说实在的我们大家在开发中确实用的不多，多数是修改内部特性，不过就是定义对象上的属性和值么？干嘛搞的这么费劲(纯属个人想法)

But在实现框架or库的时候却发挥了大用场了，这个就不多说了，只不过轻舟一片而已，还没到写库的实力

**知其然要知其所以然，来看看如何使用**

```javascript
let obj = {};
let song = '发如雪'; 
obj.singer = '周杰伦';  

Object.defineProperty(obj, 'music', {
    // 1. value: '七里香',
    configurable: true,     // 2. 可以配置对象，删除属性
    // writable: true,         // 3. 可以修改对象
    enumerable: true,        // 4. 可以枚举
    // ☆ get,set设置时不能设置writable和value，它们代替了二者且是互斥的
    get() {     // 5. 获取obj.music的时候就会调用get方法
        return song;
    },
    set(val) {      // 6. 将修改的值重新赋给song
        song = val;   
    }
});

// 下面打印的部分分别是对应代码写入顺序执行
console.log(obj);   // {singer: '周杰伦', music: '七里香'}  // 1

delete obj.music;   // 如果想对obj里的属性进行删除，configurable要设为true  2
console.log(obj);   // 此时为  {singer: '周杰伦'}

obj.music = '听妈妈的话';   // 如果想对obj的属性进行修改，writable要设为true  3
console.log(obj);   // {singer: '周杰伦', music: "听妈妈的话"}

for (let key in obj) {    
    // 默认情况下通过defineProperty定义的属性是不能被枚举(遍历)的
    // 需要设置enumerable为true才可以
    // 不然你是拿不到music这个属性的，你只能拿到singer
    console.log(key);   // singer, music    4
}

console.log(obj.music);   // '发如雪'  5
obj.music = '夜曲';       // 调用set设置新的值
console.log(obj.music);   // '夜曲'    6

```

以上是关于Object.defineProperty的用法

下面我们来写个实例看看，这里我们以Vue为参照去实现怎么写MVVM

```javascript
// index.html
<body>
    <div id="app">
        <h1>{{song}}</h1>
        <p>《{{album.name}}》是{{singer}}2005年11月发行的专辑</p>
        <p>主打歌为{{album.theme}}</p>
        <p>作词人为{{singer}}等人。</p>
        为你弹奏肖邦的{{album.theme}}
    </div>
    <!--实现的mvvm-->
    <script src="mvvm.js"></script>
    <script>
        // 写法和Vue一样
        let mvvm = new Mvvm({
            el: '#app',
            data: {     // Object.defineProperty(obj, 'song', '发如雪');
                song: '发如雪',
                album: {
                    name: '十一月的萧邦',
                    theme: '夜曲'
                },
                singer: '周杰伦'
            }
        });
    </script>
</body>

```

上面是html里的写法，相信用过Vue的同学并不陌生

那么现在就开始实现一个自己的MVVM吧

**打造MVVM**

```javascript
// 创建一个Mvvm构造函数
// 这里用es6方法将options赋一个初始值，防止没传，等同于options || {}，这就相当于默认是传入的空数组
function Mvvm(options = {}) {   
    // vm.$options Vue上是将所有属性挂载到上面
    // 所以我们也同样实现,将所有属性挂载到了$options
    this.$options = options;
    // this._data 这里也和Vue一样，这个 $options数组里面有 data这个属性，把他赋值给 data, _data
    let data = this._data = this.$options.data;
    
    // 数据劫持
    observe(data);
}

```

##### **数据劫持**

为什么要做数据劫持？

- 观察对象，给对象增加Object.defineProperty
- vue特点是不能新增不存在的属性 不存在的属性没有get和set
- 深度响应 因为每次赋予一个新对象时会给这个新对象增加defineProperty(数据劫持)

多说无益，一起看代码

```javascript
// 创建一个Observe构造函数
// 写数据劫持的主要逻辑
function Observe(data) {
    // 所谓数据劫持就是给对象增加get,set
    // 先遍历一遍对象再说
    for (let key in data) {     // 把data属性通过defineProperty的方式定义属性
        let val = data[key];
        observe(val);   // 递归继续向下找，实现深度的数据劫持
        Object.defineProperty(data, key, {
            configurable: true,
            get() {
                return val;
            },
            set(newVal) {   // 更改值的时候
                if (val === newVal) {   // 设置的值和以前值一样就不理它
                    return;
                }
                val = newVal;   // 如果以后再获取值(get)的时候，将刚才设置的值再返回去
                observe(newVal);    // 当设置为新值后，也需要把新值再去定义成属性,
              						//这里继续调用  observe就是不断地去遍历里面所有的层，给所有								层都设置上 get set 属性，就像下面的那个例子，属性里面 套着一个属性
            }
        });
    }
}

// 外面再写一个函数
// 不用每次调用都写个new
// 也方便递归调用
function observe(data) {
    // 如果不是对象的话就直接return掉
    // 防止递归溢出
    if (!data || typeof data !== 'object') return;
    return new Observe(data);
}
复制代码
```

以上代码就实现了数据劫持，不过可能也有些疑惑的地方比如：递归

再来细说一下为什么递归吧，看这个栗子

```javascript
 let mvvm = new Mvvm({
        el: '#app',
        data: {
            a: {
                b: 1
            },
            c: 2
        }
    });
复制代码
```

我们在控制台里看下（给这个 b也绑定了set get ，这就是得益于这个  递归）



![img](https://user-gold-cdn.xitu.io/2018/3/31/162797a1132d2905?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

 被标记的地方就是通过**递归**



接下来说一下observe(newVal)这里为什么也要递归

还是在可爱的控制台上，敲下这么一段代码 mvvm._data.a = {b:'ok'}

然后继续看图说话 

![img](https://user-gold-cdn.xitu.io/2018/3/31/1627983927aca52a?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

 通过observe(newVal)加上了 

![img](https://user-gold-cdn.xitu.io/2018/3/31/1627983e0e528729?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

 现在大致明白了为什么要对设置的新值也进行递归observe了吧，哈哈，so easy



数据劫持已完成，我们再做个数据代理

##### 数据代理

数据代理就是让我们每次拿data里的数据时，不用每次都写一长串，如mvvm._data.a.b这种，我们其实可以直接写成mvvm.a.b这种显而易见的方式

下面继续看下去，+号表示实现部分

```javascript
function Mvvm(options = {}) {  
    // 数据劫持
    observe(data);
    // this 代理了this._data
+   for (let key in data) {
    	//这个就是直接给全局的this绑定这个key对应的 get set
        Object.defineProperty(this, key, {
            configurable: true,
            get() {
                return this._data[key];     // 如this.a = {b: 1}
            },
            set(newVal) {
                this._data[key] = newVal;
            }
        });
+   }
}

// 此时就可以简化写法了
console.log(mvvm.a.b);   // 1
mvvm.a.b = 'ok';    
console.log(mvvm.a.b);  // 'ok'
复制代码
```

写到这里数据劫持和数据代理都实现了，那么接下来就需要编译一下了，把{{}}里面的内容解析出来

##### 数据编译

```javascript
function Mvvm(options = {}) {
    // observe(data);
        
    // 编译，el就是之前定义 块级元素的id=‘app’的    
+   new Compile(options.el, this);    
}

// 创建Compile构造函数
function Compile(el, vm) {
    // 将el挂载到实例上方便调用，此时的el值应该是“#app”
    vm.$el = document.querySelector(el);
    // 在el范围里将内容都拿到，当然不能一个一个的拿
    // 可以选择移到内存中去然后放入文档碎片中，节省开销
    let fragment = document.createDocumentFragment();
    
    while (child = vm.$el.firstChild) {
        fragment.appendChild(child);    // 此时将el中的内容放入内存中
    }
    // 对el里面的内容进行替换
    function replace(frag) {
        Array.from(frag.childNodes).forEach(node => {
            let txt = node.textContent;
            let reg = /\{\{(.*?)\}\}/g;   // 正则匹配{{}}，匹配这种mushdone的语法
            
            if (node.nodeType === 3 && reg.test(txt)) { // 即是文本节点又有大括号的情况{{}}
                console.log(RegExp.$1); // 匹配到的第一个分组 如： a.b, c
                let arr = RegExp.$1.split('.');
                let val = vm;
                arr.forEach(key => {
                    val = val[key];     // 如this.a.b
                });
                // 用trim方法去除一下首尾空格
                node.textContent = txt.replace(reg, val).trim();
            }
            // 如果还有子节点，继续递归replace
            if (node.childNodes && node.childNodes.length) {
                replace(node);
            }
        });
    }
    
    replace(fragment);  // 替换内容
    
    vm.$el.appendChild(fragment);   // 再将文档碎片放入el中
}

```

看到这里在面试中已经可以初露锋芒了，那就一鼓作气，做事做全套，来个一条龙

现在数据已经可以编译了，但是我们手动修改后的数据并没有在页面上发生改变

下面我们就来看看怎么处理，其实这里就用到了特别常见的设计模式，发布订阅模式

##### 发布订阅

发布订阅主要靠的就是数组关系，订阅就是放入函数，发布就是让数组里的函数执行

```javascript
// 发布订阅模式  订阅和发布 如[fn1, fn2, fn3]
function Dep() {
    // 一个数组(存放函数的事件池)
    this.subs = [];
}
Dep.prototype = {
    addSub(sub) {   
        this.subs.push(sub);    
    },
    notify() {
        // 绑定的方法，都有一个update方法
        this.subs.forEach(sub => sub.update());
    }
};
// 监听函数
// 通过Watcher这个类创建的实例，都拥有update方法
function Watcher(fn) {
    this.fn = fn;   // 将fn放到实例上
}
Watcher.prototype.update = function() {
    this.fn();  
};

let watcher = new Watcher(() => console.log(111));  // 
let dep = new Dep();
dep.addSub(watcher);    // 将watcher放到数组中,watcher自带update方法， => [watcher]
dep.addSub(watcher);
dep.notify();   //  111, 111
复制代码
```

##### 数据更新视图

- 现在我们要订阅一个事件，当数据改变需要重新刷新视图，这就需要在replace替换的逻辑里来处理
- 通过new Watcher把数据订阅一下，数据一变就执行改变内容的操作

```javascript
function replace(frag) {
    // 省略...
    // 替换的逻辑
    node.textContent = txt.replace(reg, val).trim();
    // 监听变化
    // 给Watcher再添加两个参数，用来取新的值(newVal)给回调函数传参
+   new Watcher(vm, RegExp.$1, newVal => {
        node.textContent = txt.replace(reg, newVal).trim();    
+   });
}

// 重写Watcher构造函数
function Watcher(vm, exp, fn) {
    this.fn = fn;
+   this.vm = vm;
+   this.exp = exp;
    // 添加一个事件
    // 这里我们先定义一个属性
+   Dep.target = this;
+   let arr = exp.split('.');
+   let val = vm;
+   arr.forEach(key => {    // 取值
+      val = val[key];     // 获取到this.a.b，默认就会调用get方法
+   });
+   Dep.target = null;
}
复制代码
```

当获取值的时候就会自动调用get方法，于是我们去找一下数据劫持那里的get方法

```javascript
function Observe(data) {
+   let dep = new Dep();
    // 省略...
    Object.defineProperty(data, key, {
        get() {
+           Dep.target && dep.addSub(Dep.target);   // 将watcher添加到订阅事件中 [watcher]
            return val;
        },
        set(newVal) {
            if (val === newVal) {
                return;
            }
            val = newVal;
            observe(newVal);
+           dep.notify();   // 让所有watcher的update方法执行即可
        }
    })
}
复制代码
```

当set修改值的时候执行了dep.notify方法，这个方法是执行watcher的update方法，那么我们再对update进行修改一下

```javascript
Watcher.prototype.update = function() {
    // notify的时候值已经更改了
    // 再通过vm, exp来获取新的值
+   let arr = this.exp.split('.');
+   let val = this.vm;
+   arr.forEach(key => {    
+       val = val[key];   // 通过get获取到新的值
+   });
    this.fn(val);   // 将每次拿到的新值去替换{{}}的内容即可
};

```

现在我们数据的更改可以修改视图了，这很good，还剩最后一点，我们再来看看面试常考的双向数据绑定吧

##### 双向数据绑定

```javascript
    // html结构
    <input v-model="c" type="text">
    
    // 数据部分
    data: {
        a: {
            b: 1
        },
        c: 2
    }
    
    function replace(frag) {
        // 省略...
+       if (node.nodeType === 1) {  // 元素节点
            let nodeAttr = node.attributes; // 获取dom上的所有属性,是个类数组
            Array.from(nodeAttr).forEach(attr => {
                let name = attr.name;   // v-model  type
                let exp = attr.value;   // c        text
                if (name.includes('v-')){
                    node.value = vm[exp];   // this.c 为 2
                }
                // 监听变化
                new Watcher(vm, exp, function(newVal) {
                    node.value = newVal;   // 当watcher触发时会自动将内容放进输入框中
                });
                
                node.addEventListener('input', e => {
                    let newVal = e.target.value;
                    // 相当于给this.c赋了一个新值
                    // 而值的改变会调用set，set中又会调用notify，notify中调用watcher的update方法实现了更新
                    vm[exp] = newVal;   
                });
            });
+       }
        if (node.childNodes && node.childNodes.length) {
            replace(node);
        }
    }
复制代码
```

大功告成，面试问Vue的东西不过就是这个罢了，什么双向数据绑定怎么实现的，问的一点心意都没有，差评！！！

**大官人请留步**，本来应该收手了，可临时起意(手痒)，再写点功能吧，再加个computed(计算属性)和mounted(钩子函数)吧

##### computed(计算属性) && mounted(钩子函数)

```javascript
    // html结构
    <p>求和的值是{{sum}}</p>
    
    data: { a: 1, b: 9 },
    computed: {
        sum() {
            return this.a + this.b;
        },
        noop() {}
    },
    mounted() {
        setTimeout(() => {
            console.log('所有事情都搞定了');
        }, 1000);
    }
    
    function Mvvm(options = {}) {
        // 初始化computed,将this指向实例
+       initComputed.call(this);     
        // 编译
        new Compile(options.el, this);
        // 所有事情处理好后执行mounted钩子函数
+       options.mounted.call(this); // 这就实现了mounted钩子函数
    }
    
    function initComputed() {
        let vm = this;
        let computed = this.$options.computed;  // 从options上拿到computed属性   {sum: ƒ, noop: ƒ}
        // 得到的都是对象的key可以通过Object.keys转化为数组
        Object.keys(computed).forEach(key => {  // key就是sum,noop
            Object.defineProperty(vm, key, {
                // 这里判断是computed里的key是对象还是函数
                // 如果是函数直接就会调get方法
                // 如果是对象的话，手动调一下get方法即可
                // 如： sum() {return this.a + this.b;},他们获取a和b的值就会调用get方法
                // 所以不需要new Watcher去监听变化了
                get: typeof computed[key] === 'function' ? computed[key] : computed[key].get,
                set() {}
            });
        });
    }
复制代码
```

写了这些内容也不算少了，最后做一个形式上的总结吧

**总结**

通过自己实现的mvvm一共包含了以下东西

1. 通过Object.defineProperty的get和set进行数据劫持
2. 通过遍历data数据进行数据代理到this上
3. 通过{{}}对数据进行编译
4. 通过发布订阅模式实现数据与视图同步
5. 通过通过通过，收了，感谢大官人的留步了

**补充**

针对以上代码在实现编译的时候还是会有一些小bug，再次经过研究和高人指点，完善了编译，下面请看修改后的代码

**修复**：两个相邻的{{}}正则匹配，后一个不能正确编译成对应的文本，如{{album.name}} {{singer}}

```javascript
function Compile(el, vm) {
    // 省略...
    function replace(frag) {
        // 省略...
        if (node.nodeType === 3 && reg.test(txt)) {
            function replaceTxt() {
                node.textContent = txt.replace(reg, (matched, placeholder) => {   
                    console.log(placeholder);   // 匹配到的分组 如：song, album.name, singer...
                    new Watcher(vm, placeholder, replaceTxt);   // 监听变化，进行匹配替换内容
                    
                    return placeholder.split('.').reduce((val, key) => {
                        return val[key]; 
                    }, vm);
                });
            };
            // 替换
            replaceTxt();
        }
    }
}
复制代码
```

上面代码主要实现依赖的是reduce方法，reduce 为数组中的每一个元素依次执行回调函数

如果还有不太清楚的，那我们单独抽出来reduce这部分再看一下

```javascript
    // 将匹配到的每一个值都进行split分割
    // 如:'song'.split('.') => ['song'] => ['song'].reduce((val, key) => val[key]) 
    // 其实就是将vm传给val做初始值，reduce执行一次回调返回一个值
    // vm['song'] => '周杰伦'
    
    // 上面不够深入，我们再来看一个
    // 再如：'album.name'.split('.') => ['album', 'name'] => ['album', 'name'].reduce((val, key) => val[key])
    // 这里vm还是做为初始值传给val，进行第一次调用，返回的是vm['album']
    // 然后将返回的vm['album']这个对象传给下一次调用的val
    // 最后就变成了vm['album']['name'] => '十一月的萧邦'
    
    return placeholder.split('.').reduce((val, key) => {
        return val[key]; 
    }, vm);
复制代码
```

reduce的用处多多，比如计算数组求和是比较普通的方法了，还有一种比较好用的妙处是可以进行二维数组的展平(flatten)，各位不妨来看最后一眼

```javascript
let arr = [
  [1, 2],
  [3, 4],
  [5, 6]
];

let flatten = arr.reduce((previous, current) => {
  return previous.concat(current);
});

console.log(flatten); // [1, 2, 3, 4, 5, 6]

// ES6中也可以利用...展开运算符来实现的，实现思路一样，只是写法更精简了
flatten = arr.reduce((a, b) => [...a, ...b]);
console.log(flatten); // [1, 2, 3, 4, 5, 6]
```














## 二、Vue 为什么不能检测数组和对象的变化,怎么处理

(为什么通过索引操作数组不能触发响应式)

首先这个在景行实习期间的vue 指导手册里面，就提到了





### 

![1612147746846](C:\Users\sz\AppData\Roaming\Typora\typora-user-images\1612147746846.png)

[https://cn.vuejs.org/v2/guide/reactivity.html#%E6%A3%80%E6%B5%8B%E5%8F%98%E5%8C%96%E7%9A%84%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9](https://cn.vuejs.org/v2/guide/reactivity.html#检测变化的注意事项)

先看看 demo，

```javascript
<template>
  <div>
    <button @click="btnclick()">点击</button>
    <div>{{this.arr}}</div>
  </div>
</template>
<script>
export default {
  name: "Home",
  data() {
    return {
      arr:[1,2,3]
    };
  },
  methods:{
    btnclick(){
      console.log('点击了');
      //通过splice就可以解决这个问题
      //this.arr.splice(1,1,12)
      this.arr[1] = 12;
      console.log(this.arr);
    }
  }
};
</script>

```

![1612148159542](C:\Users\sz\AppData\Roaming\Typora\typora-user-images\1612148159542.png)

点击之后，发现arr数组里面 确实是改变了第二个元素。

![1612148180809](C:\Users\sz\AppData\Roaming\Typora\typora-user-images\1612148180809.png)

但是去看看 view 界面的情况，发现还是没有改变的，说明 model层是发生了变化，但是view层没有更新

![1612148159542](C:\Users\sz\AppData\Roaming\Typora\typora-user-images\1612148159542.png)

这个也是一个数组变化，但是检测不到的例子 ： https://juejin.cn/post/6844904030712365069

还有下面的代码，想对这个对象进行改变，发现也是改变不了，最后和上面一样，控制台打印

![1612229577199](C:\Users\sz\AppData\Roaming\Typora\typora-user-images\1612229577199.png)

控制台打印如下：添加了c ，但是c没有get set 属性，所以界面还是没有发生变化。

![1612229723778](C:\Users\sz\AppData\Roaming\Typora\typora-user-images\1612229723778.png)

下面是产生这种情况的具体原因：https://juejin.cn/post/6918652111542714381

### **1.数组不能被检测变化**



#### 不能检测的原因

**起源**	

研究Vue响应式原理时遇到一个问题，为什么Vue不能检测到以元素赋值方式的数组变动？

Vue的官方文档说：

> 由于 JavaScript 的限制，Vue 不能检测以下数组的变动：
>
> 1. 当你利用索引直接设置一个数组项时，例如：vm.items[indexOfItem] = newValue
> 2. 当你修改数组的长度时，例如：vm.items.length = newLength

> 为了解决第一类问题，以下两种方式都可以实现和 vm.items[indexOfItem] = newValue 相同的效果，同时也将在响应式系统内触发状态更新：

```javascript
// Vue.set
Vue.set(vm.items, indexOfItem, newValue)
复制代码
// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)
复制代码
```

> 你也可以使用 `vm.$set` 实例方法，该方法是全局方法 `Vue.set` 的一个别名：

```javascript
vm.$set(vm.items, indexOfItem, newValue)
复制代码
```

> 为了解决第二类问题，你可以使用 `splice`：

```javascript
vm.items.splice(newLength)
复制代码
```

什么叫“由于 JavaScript 的限制”？这很困扰我，数组也是对象，就不能对数组的项也做劫持么？

**思考**

Vue的双向绑定是通过 `Object.defineProperty` 给对象添加 `getter` 和 `setter` 方法实现的。

```javascript
var data = {
    name: 'Kikky',
    msg: 'hello'
}

// 枚举data里的属性并添加getter setter方法
Object.keys(data).forEach(function(key) {
    Object.defineProperty(data, key, {
        get: function() {
            console.log('trigger subscription')
        },
        set: function() { // 属性变动触发通知
            console.log('trigger notify')
        }
    })
})

data.msg = 'hello world' // 输出 trigger notify
复制代码
```

对象可以，那么数组是否也可以呢？试一下

```javascript
var array = ['a', 'b']

// 枚举数组各项，试图设置各项的getter，setter，
for (var i = 0, len = array.length; i < len ;i++) {
    // 数组的index就相当于对象的属性
    Object.defineProperty(array, i, {
        get: function() {
            console.log('trigger subscription')
        },
        set: function() { // 数组项变动触发通知
            console.log('trigger notify')
        }
    })
}
//这个时候，可以看到也是可以触发这个 set方法的
array[0] = 'x' // // 输出 trigger notify

```

事实证明，是可以通过 `array[index] = newValue` 这样的方式触发响应的。**那Vue为什么不这样做呢？**

试想一下，如果数组的长度不是2个，而是100， 1000，要是每个元素都这样设置一遍，是不是很笨拙，也很损耗性能？

不仅如此，由于JavaScript的数组是可变的，可以通过 `array[index] = value` 随时添加数组项，而Object.defineProperty是针对已有项的设置，新加的项是不会被 `Object.definePropert`设置的，也就不会触发响应了。对象可以没影响是因为我们在创建Vue实例的时候，data中的属性是预先定义好了的。

所以，所谓的“**由于 JavaScript 的限制**”，是因为动态添加的数组项不能被劫持而产生响应。

实际上，Vue对数组项的操作方法（`pop, push, shifut, unshift, splice, sort, reverse`）做了重写，这些方法可以改变数组，而不用管某一具体的数组项了，更加灵活。

**总结**

Vue不能检测到以元素赋值方式的数组变动是因为：

1. 动态添加的数组项不能被劫持生成getter, setter，因此无法产生响应。
2. 给数组每一项做劫持，性能低且笨拙。





#### 数组数据是怎么被监听的

我们知道，上面是对对象的数据进行监听的，我们不能对数组进行数据的“劫持”。那么Vue是怎么做的呢？

```javascript
import { def } from '../util/index'

const arrayProto = Array.prototype
export const arrayMethods = Object.create(arrayProto)
//下面是需要修改的 方法名
const methodsToPatch = [
  'push',
  'pop',
  'shift',
  'unshift',
  'splice',
  'sort',
  'reverse'
]


methodsToPatch.forEach(function (method) {
  // 缓存原来的方法
  const original = arrayProto[method]
  def(arrayMethods, method, function mutator (...args) {
    const result = original.apply(this, args)
    const ob = this.__ob__
    let inserted
    switch (method) {
      case 'push':
      case 'unshift':
        inserted = args
        break
      case 'splice':
        inserted = args.slice(2)
        break
    }
    if (inserted) ob.observeArray(inserted)
    // notify change
    ob.dep.notify()
    return result
  })
})


```

看来Vue能对数组进行监听的原因是，把数组的方法重写了。总结起来就是这几步：

**01**先获取原生 Array 的原型方法，因为拦截后还是需要原生的方法帮我们实现数组的变化。

**02**对 Array 的原型方法使用 Object.defineProperty 做一些拦截操作。

**03**把需要被拦截的 Array 类型的数据原型指向改造后原型。

#### Vue为什么不能检测数组变动

并不是说 JS 不能支持响应式数组，其实JS是没有这种限制的。

数组在 JS 中常被当作栈，队列，集合等数据结构的实现方式，会有批量的数据以待遍历。并且 runtime 对对象与数组的优化也有所不同。

所以对数组的处理需要特化出来以提高性能。

Vue 中是通过对每个键设置 getter/setter 来实现响应式的，开发者使用数组，目的往往是遍历，此时调用 getter 开销太大了，所以 Vue 不在数组每个键上设置，而是在数组上定义 `__ob__` ，并且替换了 push 等等能够影响原数组的原型方法。

为此也有人去GitHub问了尤大，他的回答也是说因为性能问题而没有采用这种方式监听数组。https://github.com/vuejs/vue/issues/8562

源码位置：**src/core/observer/index.js**

```
constructor (value: any) {
    this.value = value
    this.dep = new Dep()
    this.vmCount = 0
    def(value, '__ob__', this)
    if (Array.isArray(value)) {
      if (hasProto) {
        protoAugment(value, arrayMethods)
      } else {
        copyAugment(value, arrayMethods, arrayKeys)
      }
      this.observeArray(value)
    } else {
      this.walk(value)
    }
  }

  /**
   * Walk through all properties and convert them into
   * getter/setters. This method should only be called when
   * value type is Object.
   */
  walk (obj: Object) {
    const keys = Object.keys(obj)
    for (let i = 0; i < keys.length; i++) {
      defineReactive(obj, keys[i])
    }
  }
复制代码
```

通过源码我们可以知道，Vue 没有对数组每个键设置响应式的过程，而是直接对值进行递归设置响应式。



#### **$set为啥能检测数组变动**

还是去源码瞅一眼，看vue是怎么对数组进行处理的。

源码位置：**dist/vue.runtime.esm.js**

```
function set (target, key, val) {
  //... 
  if (Array.isArray(target) && isValidArrayIndex(key)) {
    target.length = Math.max(target.length, key);
    target.splice(key, 1, val);
    return val
  }
  if (key in target && !(key in Object.prototype)) {
    target[key] = val;
    return val
  }
  //... 
  defineReactive$$1(ob.value, key, val);
  ob.dep.notify();
  return val
}
复制代码
```

------

- 如果target是一个数组且索引有效，就设置length的属性。
- 通过splice方法把value设置到target数组指定位置。
- 设置的时候，vue会拦截到target发生变化，然后把新增的value也变成响应式
- 最后返回value

这就是vue重写数组方法的原因，利用数组这些方法触发使得每一项的value都是响应式的。


作者：前端小时链接：https://juejin.cn/post/6918652111542714381来源：掘金著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



### 2.为什么Vue3.0不再使用defineProperty

Vue3.0 中，响应式数据部分弃用了 `Object.defineProperty`，使用`Proxy`来代替它。本文将主要通过以下三个方面来分析为什么 Vue 选择弃用 `Object.defineProperty`。

1. `Object.defineProperty` 真的无法监测数组下标的变化吗？
2. 分析 Vue2.x 中对数组 `Observe` 部分源码。
3. 对比`Object.defineProperty`和 `Proxy`。

无法监控到数组下标的变化？

在一些技术博客上，我看到过这样一种说法，认为 `Object.defineProperty` 有一个缺陷是无法监听数组变化：

> 无法监控到数组下标的变化，导致直接通过数组的下标给数组设置值，不能实时响应。所以 Vue 才设置了 7 个变异数组（`push、pop、shift、unshift、splice、sort、reverse`）的 hack 方法来解决问题。
>
> `Object.defineProperty`的第一个缺陷是无法监听数组变化。然而 Vue 的文档提到了 Vue 是可以检测到数组变化的，但是只有以下八种方法，vm.items[indexOfItem] = newValue 这种是无法检测的。

这种说法是有问题的，事实上，`Object.defineProperty` 本身是可以监控到数组下标的变化的，只是在 Vue 的实现中，从性能 / 体验的性价比考虑，放弃了这个特性。

下面我们通过一个例子来为` Object.defineProperty `正名：

```javascript
function defineReactive(data, key, value) {
  Object.defineProperty(data, key, {
    enumerable: true,
    configurable: true,
     get: function defineGet() {
      console.log(`get key: ${key} value: ${value}`)
      return value
    },
     set: function defineSet(newVal) {
      console.log(`set key: ${key} value: ${newVal}`)
      value = newVal
    }
  })
}
function observe(data) {
  Object.keys(data).forEach(function(key) {
    defineReactive(data, key, data[key])
  })
}
let arr = [1, 2, 3]
observe(arr)
```

上面的代码对数组 arr 的每个属性通过 `Object.defineProperty` 进行劫持，下面我们对数组 arr 进行操作，看看哪些行为会触发数组的 `getter` 和 `setter` 方法。

**1. 通过下标获取某个元素和修改某个元素的值**

![图片](https://mmbiz.qpic.cn/mmbiz_png/XIibZ0YbvibkVTs4Fpzl9OsLQqmQgZy7WYqIRrytTBbInrSZEnAUM9TVNe5X8gT7R6SumVue1yLbsVAhmsmh8ABg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

可以看到，通过下标获取某个元素会触发 `getter` 方法, 设置某个值会触发 setter 方法。

接下来，我们再试一下数组的一些操作方法，看看是否会触发。

**2. 数组的 `push` 方法**

![图片](https://mmbiz.qpic.cn/mmbiz_png/XIibZ0YbvibkVTs4Fpzl9OsLQqmQgZy7WYDpELzvM1w9LVrwelqUjaCpkicMhYeHUlRHg1Cg3355QTSgV9yHiaTicDg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

`push` 并未触发 `setter` 和 `getter`方法，数组的下标可以看做是对象中的 `key` ，这里 `push` 之后相当于增加了下索引为 3 的元素，但是并未对新的下标进行 `observe` ，所以不会触发。

**3. 数组的 unshift 方法**

![图片](https://mmbiz.qpic.cn/mmbiz_png/XIibZ0YbvibkVTs4Fpzl9OsLQqmQgZy7WYTALsjpGrxduicWUMk7sgdQeGtRsU067CROhedjEZINgcNIZBDMCERZg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

我擦，发生了什么？

unshift 操作会导致原来索引为 0、1、2、3 的值发生变化，这就需要将原来索引为 0、1、2、3 的值取出来，然后重新赋值，所以取值的过程触发了 `getter` ，赋值时触发了 setter 。

下面我们尝试通过索引获取一下对应的元素：

![图片](https://mmbiz.qpic.cn/mmbiz_png/XIibZ0YbvibkVTs4Fpzl9OsLQqmQgZy7WY8lZy4QMr5DmTqtWibV3HnrJC5UFUN5zltggzWE0gN9icGnIaPOYicAW9A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

只有索引为 0、1、2 的属性才会触发 `getter` 。

这里我们可以对比对象来看，arr 数组初始值为 [1, 2, 3]，即只对索引为 0，1，2 执行了 observe 方法，所以无论后来数组的长度发生怎样的变化，依然只有索引为 0、1、2 的元素发生变化才会触发。其他的新增索引，就相当于对象中新增的属性，需要再手动 observe 才可以。

**4. 数组的 pop 方法**

![图片](https://mmbiz.qpic.cn/mmbiz_png/XIibZ0YbvibkVTs4Fpzl9OsLQqmQgZy7WYsNfzoRPd8WyuGAiabMQicpNl0H0SGkSXJtCuD5AeXRpsvibMl45UonKTQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

当移除的元素为引用为 2 的元素时，会触发 `getter` 。

![图片](https://mmbiz.qpic.cn/mmbiz_png/XIibZ0YbvibkVTs4Fpzl9OsLQqmQgZy7WY3kTZzTuaibx0Gz9ibmHNt3s5WgUlY9FIkk93OianITAz0gMbsKAghvCIw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

删除了索引为 2 的元素后，再去修改或获取它的值时，不会再触发 `setter` 和 `getter` 。

这和对象的处理是同样的，数组的索引被删除后，就相当于对象的属性被删除一样，不会再去触发 observe。

**到这里，我们可以简单地总结一下结论。**

`Object.defineProperty` 在数组中的表现和在对象中的表现是一致的，数组的索引就可以看做是对象中的 `key`。

1. 通过索引访问或设置对应元素的值时，可以触发 `getter` 和 `setter` 方法。
2. 通过 `push` 或 `unshift` 会增加索引，对于新增加的属性，需要再手动初始化才能被 observe。
3. 通过 pop 或 shift 删除元素，会删除并更新索引，也会触发 `setter` 和 `getter` 方法。

所以，`Object.defineProperty`是有监控数组下标变化的能力的，只是 Vue2.x 放弃了这个特性。

 Vue 对数组的 observe 做了哪些处理？

Vue 的 Observer 类定义在 core/observer/index.js 中。

![图片](https://mmbiz.qpic.cn/mmbiz_png/XIibZ0YbvibkVTs4Fpzl9OsLQqmQgZy7WYRE5gF3kCWXlXCtQstu1FoOpmQ74Jn4gQ2yYCcD14oibqUtUZ7m6XxrQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

可以看到，Vue 的 Observer 对数组做了单独的处理。

![图片](https://mmbiz.qpic.cn/mmbiz_png/XIibZ0YbvibkVTs4Fpzl9OsLQqmQgZy7WYUdaEJjqw1Kn9CtOXNL1KWKrzXCZwgbsMYPevTslajBLK8GPXUQ22Ug/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

hasProto 判断数组的实例是否有 **proto** 属性，如果有 **proto** 属性就会执行 protoAugment 方法，将 arrayMethods 重写到原型上。hasProto 的定义如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/XIibZ0YbvibkVTs4Fpzl9OsLQqmQgZy7WYmFvCFoUWaS8p3JtiaVrM6yKdCS2xV8bouhrms4rY7PzWncctKwkomWQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

arrayMethods 是对数组的方法进行重写，定义在 core/observer/array.js 中，下面是这部分源码的分析：

```javascript
/*
 * not type checking this file because flow doesn't play well with
 * dynamically accessing methods on Array prototype
 */
import { def } from '../util/index'
// 复制数组构造函数的原型，Array.prototype 也是一个数组。
const arrayProto = Array.prototype
// 创建对象，对象的 __proto__ 指向 arrayProto，所以 arrayMethods 的 __proto__ 包含数组的所有方法。
export const arrayMethods = Object.create(arrayProto)
// 下面的数组是要进行重写的方法
const methodsToPatch = [
  'push',
  'pop',
  'shift',
  'unshift',
  'splice',
  'sort',
  'reverse'
]
/**
 * Intercept mutating methods and emit events
 */
// 遍历 methodsToPatch 数组，对其中的方法进行重写
methodsToPatch.forEach(function (method) {
  // cache original method
  const original = arrayProto[method]
  // def 方法定义在 lang.js 文件中，是通过 object.defineProperty 对属性进行重新定义。
  // 即在 arrayMethods 中找到我们要重写的方法，对其进行重新定义
  def(arrayMethods, method, function mutator (...args) {
    const result = original.apply(this, args)
    const ob = this.__ob__
    let inserted
    switch (method) {
      // 上面已经分析过，对于 push，unshift 会新增索引，所以需要手动 observe
      case 'push':
      case 'unshift':
        inserted = args
        break
      // splice 方法，如果传入了第三个参数，也会有新增索引，所以也需要手动 observe
      case 'splice':
        inserted = args.slice(2)
        break
    }
    // push，unshift，splice 三个方法触发后，在这里手动 observe，其他方法的变更会在当前的索引上进行更新，所以不需要再执行 ob.observeArray
    if (inserted) ob.observeArray(inserted)
    // notify change
    ob.dep.notify()
    return result
  })
})
```

`Object.defineProperty` VS Proxy

上面已经知道 `Object.defineProperty` 对数组和对象的表现是一致的，那么它和 Proxy 对比存在哪些优缺点呢？

**1. `Object.defineProperty `只能劫持对象的属性，而 Proxy 是直接代理对象。**

由于 `Object.defineProperty` 只能对属性进行劫持，需要遍历对象的每个属性，如果属性值也是对象，则需要深度遍历。而 Proxy 直接代理对象，不需要遍历操作。

**2. `Object.defineProperty `对新增属性需要手动进行 Observe。**

由于 `Object.defineProperty `劫持的是对象的属性，所以新增属性时，需要重新遍历对象，对其新增属性再使用 `Object.defineProperty` 进行劫持。

也正是因为这个原因，使用 Vue 给 data 中的数组或对象新增属性时，需要使用 vm.$set 才能保证新增的属性也是响应式的。

下面看一下 Vue 的 set 方法是如何实现的，set 方法定义在 core/observer/index.js ，下面是核心代码。

```javascript
/**
 * Set a property on an object. Adds the new property and
 * triggers change notification if the property doesn't
 * already exist.
 */
export function set (target: Array<any> | Object, key: any, val: any): any {
  // 如果 target 是数组，且 key 是有效的数组索引，会调用数组的 splice 方法，
  // 我们上面说过，数组的 splice 方法会被重写，重写的方法中会手动 Observe
  // 所以 vue 的 set 方法，对于数组，就是直接调用重写 splice 方法
  if (Array.isArray(target) && isValidArrayIndex(key)) {
    target.length = Math.max(target.length, key)
    target.splice(key, 1, val)
    return val
  }
  // 对于对象，如果 key 本来就是对象中的属性，直接修改值就可以触发更新
  if (key in target && !(key in Object.prototype)) {
    target[key] = val
    return val
  }
  // vue 的响应式对象中都会添加了 __ob__ 属性，所以可以根据是否有 __ob__ 属性判断是否为响应式对象
  const ob = (target: any).__ob__
  // 如果不是响应式对象，直接赋值
  if (!ob) {
    target[key] = val
    return val
  }
  // 调用 defineReactive 给数据添加了 getter 和 setter，
  // 所以 vue 的 set 方法，对于响应式的对象，就会调用 defineReactive 重新定义响应式对象，defineReactive 函数
  defineReactive(ob.value, key, val)
  ob.dep.notify()
  return val
}
```

在 set 方法中，对 target 是数组和对象分别做了处理。target 是数组时，会调用重写过的 splice 方法进行手动 Observe 。

对于对象，如果 `key` 本来就是对象的属性，则直接修改值触发更新，否则调用 defineReactive 方法重新定义响应式对象。

如果采用 `proxy` 实现，`Proxy` 通过 `set(target, propKey, value, receiver)` 拦截对象属性的设置，是可以拦截到对象的新增属性的。

![图片](https://mmbiz.qpic.cn/mmbiz_png/XIibZ0YbvibkVTs4Fpzl9OsLQqmQgZy7WYicMXOja2q16hJJvibYXaKlmtMTzksrpEKc2TJVqKKP4oO1Rvb9Ftjia0g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

不止如此，Proxy 对数组的方法也可以监测到，不需要像上面 vue2.x 源码中那样进行 hack。

![图片](https://mmbiz.qpic.cn/mmbiz_png/XIibZ0YbvibkVTs4Fpzl9OsLQqmQgZy7WYpibm7ekwOn00ibicKRMdo39ya09NlnlWlw3tRsjibejZrJX3oUAFOENlRw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

完美！！！

**3. `Proxy`支持 13 种拦截操作，这是 `defineProperty `所不具有的。**

- **`get(target, propKey, receiver)`**：拦截对象属性的读取，比如 `proxy.foo` 和`proxy['foo']`。
- **`set(target, propKey, value, receiver)`**：拦截对象属性的设置，比如`proxy.foo = v` 或 `proxy['foo'] = v`，返回一个布尔值。
- **`has(target, propKey)`**：拦截 `propKey in proxy` 的操作，返回一个布尔值。
- **`deleteProperty(target, propKey)`**：拦截 `delete proxy[propKey]` 的操作，返回一个布尔值。
- **`ownKeys(target)`**：拦截`Object.getOwnPropertyNames(proxy)、Object.getOwnPropertySymbols(proxy)、Object.keys(proxy)、for...in`循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而 `Object.keys()` 的返回结果仅包括目标对象自身的可遍历属性。
- **`getOwnPropertyDescriptor(target, propKey)`**：拦截`Object.getOwnPropertyDescriptor(proxy, propKey)`，返回属性的描述对象。
- **`defineProperty(target, propKey, propDesc)`**：拦截`Object.defineProperty(proxy, propKey, propDesc）`、`Object.defineProperties(proxy, propDescs)`，返回一个布尔值。
- **`preventExtensions(target)`**：拦截 `Object.preventExtensions(proxy)`，返回一个布尔值。
- **`getPrototypeOf(target)`**：拦截 `Object.getPrototypeOf(proxy)`，返回一个对象。
- **`isExtensible(target)`**：拦截 `Object.isExtensible(proxy)`，返回一个布尔值。
- **`setPrototypeOf(target, proto)`**：拦截 `Object.setPrototypeOf(proxy, proto)`，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
- **`apply(target, object, args)`**：拦截 `Proxy` 实例作为函数调用的操作，比如`proxy(...args)、proxy.call(object, ...args)、proxy.apply(...)`。
- **`construct(target, args)`**：拦截 `Proxy` 实例作为构造函数调用的操作，比如`new proxy(...args)`。

**4. 新标准性能红利**

`Proxy` 作为新标准，从长远来看，JS 引擎会继续优化 `Proxy`，但 `getter` 和 `setter` 基本不会再有针对性优化。

 **总 结**

1. `Object.defineProperty` 并非不能监控数组下标的变化，Vue2.x 中无法通过数组索引来实现响应式数据的自动更新是 Vue 本身的设计导致的，不是 defineProperty 的锅。
2. `Object.defineProperty` 和 `Proxy` 本质差别是，`defineProperty` 只能对属性进行劫持，所以出现了需要递归遍历，新增属性需要手动 `Observe` 的问题。
3. `Proxy` 作为新标准，浏览器厂商势必会对其进行持续优化，但它的兼容性也是块硬伤，并且目前还没有完整的 polyfill 方案。





## 三、父子组件间传值



**只需要记住两句话**

父组件通过 v一bind 向子组件传值，子组件通过props来获取父组件传涕过来的值。

在子组件中，通过 $emit （）  来触发事件。在父组件中，通过 v-on 来监听子组件事件·



[![RcXsnP.png](https://z3.ax1x.com/2021/07/02/RcXsnP.png)](https://imgtu.com/i/RcXsnP)
[![RcXDXt.png](https://z3.ax1x.com/2021/07/02/RcXDXt.png)](https://imgtu.com/i/RcXDXt)
[![RcXB6I.png](https://z3.ax1x.com/2021/07/02/RcXB6I.png)](https://imgtu.com/i/RcXB6I)
[![RcX01A.png](https://z3.ax1x.com/2021/07/02/RcX01A.png)](https://imgtu.com/i/RcX01A)

[![RcXqNF.png](https://z3.ax1x.com/2021/07/02/RcXqNF.png)](https://imgtu.com/i/RcXqNF)











## 四、父子组件间的访问方式





[![RcztKA.png](https://z3.ax1x.com/2021/07/02/RcztKA.png)](https://imgtu.com/i/RcztKA)
[![Rcz3CD.png](https://z3.ax1x.com/2021/07/02/Rcz3CD.png)](https://imgtu.com/i/Rcz3CD)












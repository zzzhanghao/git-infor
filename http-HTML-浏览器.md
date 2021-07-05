# 网络/http

### 1、cookie、session、localStorage分别是什么？有什么作用？



参考逼站  视频 https://www.bilibili.com/video/BV1ut411j7R7?from=search&seid=8756659634866227661



![image-20210114215048170](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210114215048170.png)

同域名就是 在一个浏览器下 打开两个百度网址，这两个都是一个域名的

跨域就是 不同浏览器了





**1. 第一种解释**



**一、cookie**

1. cookie是存储在浏览器上的一小段数据，用来记录某些当页面关闭或者刷新后仍然需要记录的信息。在控制台用 「document.cookie」查看你当前正在浏览的网站的cookie。
2. cookie可以使用 js 在浏览器直接设置（用于记录不敏感信息，如用户名）, 也可以在服务端通使用 HTTP 协议规定的 set-cookie 来让浏览器种下cookie，这是最常见的做法。（打开一个网站，清除全部cookie，然后刷新页面，在network的Response headers试试找一找set-cookie吧）
3. 每次网络请求 Request headers 中都会带上cookie。所以如果 cookie 太多太大对传输效率会有影响。
4. 一般浏览器存储cookie 最大容量为4k，所以大量数据不要存到cookie。
5. 设置cookie时的参数：



- - path：表示 cookie 影响到的路径，匹配该路径才发送这个 cookie。expires 和 maxAge：告诉浏览器 cookie 时候过期，maxAge 是 cookie 多久后过期的相对时间。不设置这两个选项时会产生 session cookie，session cookie 是 transient 的，当用户关闭浏览器时，就被清除。一般用来保存 session 的 session_id。
  - secure：当 secure 值为 true 时，cookie 在 HTTP 中是无效，在 HTTPS 中才有效
  - httpOnly：浏览器不允许脚本操作 document.cookie 去更改 cookie。一般情况下都应该设置这个为 true，这样可以避免被 xss 攻击拿到 cookie。[[cookie 参数\]](https://link.zhihu.com/?target=https%3A//github.com/alsotang/node-lessons/tree/master/lesson16)[[简述 Cookie 是什么\]](https://zhuanlan.zhihu.com/p/22396872?refer=study-fe)





**二、session**



1. 当一个用户打开淘宝登录后，刷新浏览器仍然展示登录状态。服务器如何分辨这次发起请求的用户是刚才登录过的用户呢？这里就使用了session保存状态。用户在输入用户名密码提交给服务端，服务端验证通过后会创建一个session用于记录用户的相关信息，这个 session 可保存在服务器内存中，也可保存在数据库中。	

- 创建session后，会把关联的session_id 通过setCookie 添加到http响应头部中。
- 浏览器在加载页面时发现响应头部有 set-cookie字段，就把这个cookie 种到浏览器指定域名下。
- 当下次刷新页面时，发送的请求会带上这条cookie， 服务端在接收到后根据这个session_id来识别用户。
- cookie 是存储在浏览器里的一小段「数据」，而session是一种让服务器能识别某个用户的「机制」，session 在实现的过程中需要使用cookie。当然有时候说到 session 也指服务器里创建的那个和用户身份关联的对象。

**三、localStorage**

1. localStorage HTML5本地存储web storage特性的API之一，用于将大量数据（最大5M）保存在浏览器中，保存后数据永远存在不会失效过期，除非用 js手动清除。
2. 不参与网络传输。
3. 一般用于性能优化，可以保存图片、js、css、html 模板、大量数据。





- ### `localStorage，sessionStorage和cookie的区别`

> **共同点**：都是保存在浏览器端、且同源的

- ```
  数据存储方面
  ```

  - **cookie数据**始终在同源的http请求中携带（即使不需要），即cookie在浏览器和服务器间来回传递。cookie数据还有路径（path）的概念，可以限制cookie只属于某个路径下
  - **sessionStorage和localStorage**不会自动把数据发送给服务器，仅在**本地保存**。

- ```
  存储数据大小
  ```

  - 存储大小限制也不同，**cookie数据**不能超过4K，同时因为每次http请求都会携带cookie、所以cookie只适合保存很小的数据，如会话标识。
  - **sessionStorage和localStorage**虽然也有存储大小的限制，但比cookie大得多，可以达到5M或更大

- ```
  数据存储有效期
  ```

  - **sessionStorage**：仅在当前浏览器窗口关闭之前有效；
  - **localStorage**：始终有效，窗口或浏览器关闭也一直保存，本地存储，因此用作持久数据；
  - **cookie**：只在设置的cookie过期时间之前有效，即使窗口关闭或浏览器关闭

- ```
  作用域不同
  ```

  - **sessionStorage**不在不同的浏览器窗口中共享，即使是同一个页面；
  - **localstorage**在所有`同源窗口`中都是共享的；也就是说只要浏览器不关闭，数据仍然存在
  - **cookie**: 也是在所有`同源窗口`中都是共享的.也就是说只要浏览器不关闭，数据仍然存在





**2. 第一种解释**

***什么是cookie?***



由于**HTTP是一种无状态的协议**，服务器单从网络连接上是无法知道客户身份的。这时候服务器就需要给客户端颁发一个cookie，用来确认用户的身份。

简单的说，cookie就是客户端保存用户信息的一种机制，用来记录用户的一些信息。

> 原理:web服务器通过在http响应消息头增加Set-Cookie响应头字段将Cookie信息发送给浏览器，浏览器则通过在http请求消息中增加Cookie请求头字段将Cookie回传给web服务器。

***cookie的构成***

服务器端向客户端发送Cookie是通过HTTP响应报文实现的，在Set-Cookie中设置需要向客户端发送的cookie，cookie格式如下：

```
Set-Cookie: "name=value;domain=.domain.com;path=/;expires=Sat, 11 Jun 2019 11:29:42 GMT;HttpOnly;secure"
复制代码
```

其中`name=value`是必选项，其它都是可选项。Cookie的主要构成如下：

- name:一个唯一确定的cookie名称。通常来讲cookie的名称是不区分大小写的。
- value:存储在cookie中的字符串值。**最好为cookie的name和value进行url编码**
- domain:cookie对于哪个域是有效的。所有向该域发送的请求中都会包含这个cookie信息。这个值可以包含子域(如：e.baidu.com)，也可以不包含它(如：.baidu.com，则对于baidu.com的所有子域都有效)。
- path: 表示这个cookie影响到的路径，浏览器跟会根据这项配置，像指定域中匹配的路径发送cookie。
- expires:失效时间，表示cookie何时应该被删除的时间戳(也就是，何时应该停止向服务器发送这个cookie)。如果不设置这个时间戳，浏览器会在页面关闭时即将删除所有cookie；不过也可以自己设置删除时间。这个值是GMT时间格式。**如果客户端和服务器端时间不一致，使用expires就会存在偏差。并且如果给cookie设置一个过去的时间，浏览器会立即删除该cookie**
- max-age: 与expires作用相同，用来告诉浏览器此cookie多久过期（单位是秒），而不是一个固定的时间点。正常情况下，max-age的优先级高于expires。
- HttpOnly: 告知浏览器不允许通过脚本`document.cookie`去更改这个值，同样这个值在document.cookie中也不可见。但在http请求张仍然会携带这个cookie。注意这个值虽然在脚本中不可获取，但仍然在浏览器安装目录中以文件形式存在。这项设置通常在服务器端设置。
- secure: 安全标志，指定后，只有在使用SSL链接时候才能发送到服务器，如果是http链接则不会传递该信息。

> 这里强调一点，是**Cookie的不可跨域名性**
> 很多网站都会使用Cookie，不同浏览器采用不同的方式保存Cookie，而且每个网站的Cookie只能够被对应的网站使用。意思就是说当浏览器访问baidu时，只会带baidu的Cookie，而不会带其他网站的Cookie，这就是Cookie的不可跨域名性 。 Cookie在客户端是由浏览器来管理的。浏览器可以保证各个网站只能操作各个网站的Cookie，从而保证用户的隐私安全。

***cookie的特点***

**Cookie并不提供修改、删除操作**

如果要修改某个Cookie，只需要新建一个同名的Cookie，添加到response中覆盖原来的Cookie。

如果要删除某个Cookie，只需要新建一个同名的Cookie，并将maxAge设置为0，并添加到response中覆盖原来的Cookie。**注意是0而不是负数。负数代表其他的意义。**

**注意：修改、删除Cookie时，新建的Cookie除value、maxAge之外的所有属性，例如name、path、domain等，都要与原Cookie完全一样。否则，浏览器将视为两个不同的Cookie不予覆盖，导致修改、删除失败。**

***session***



***什么是session?***

Session是另一种记录客户状态的机制，不同的是Cookie保存在客户端浏览器中，而Session保存在服务器上。客户端浏览器访问服务器的时候，服务器把客户端信息以某种形式记录在服务器上。
客户端浏览器再次访问时只需要从该Session中查找该客户的状态就可以了

**session的工作步骤**

因为HTTP协议是无状态的，Session不能依据HTTP连接来判断是否为同一个用户。于是服务器向用户的浏览器发送了一个名为JESSIONID的Cookie，它的值是Session的id值。这个id可以让Session依据Cookie来识别是否是同一个用户。

简单来说：Session 之所以可以识别不同的用户，依靠的就是Cookie，**所以说session是基于Cookie的**

该Cookie是服务器自动颁发给浏览器的，不用我们手工创建的。该Cookie的maxAge值默认是-1，也就是说仅当前浏览器使用，不将该Cookie存在硬盘中，并且各浏览器窗口间不共享，关闭浏览器就会失效。

**工作步骤：**
将客户端称为 client，服务端称为 server

1. 产生 sessionID：session 是基于 cookie 的一种方案，所以，首先要产生 cookie。client 第一次访问 server，server 生成一个随机数，命名为 sessionID，并将其放在响应头里，以 cookie 的形式返回给 client，client 以处理其他 cookie 的方式处理这段 cookie。大概是这样：`cookie：sessionID=135165432165`
2. 保存 sessionID： server 将要保存的数据保存在相对应的 sessionID 之下，再将 sessionID 保存到服务器端的特定的保存 session 的内存中（如 一个叫 session 的哈希表）
3. 使用 session： client 再次访问 server，会带上首次访问时获得的 值为 sessionID 的cookie，server 读取 cookie 中的 sessionID，根据 sessionID 到保存 session 的内存寻找与 sessionID 匹配的数据，若寻找成功就将数据返回给 client。

**session的有效期**

Session保存在服务器端。为了获得更高的存取速度，服务器一般把Session放在内存里。每个用户都会有一个独立的Session。如果Session内容过于复杂，当大量客户访问服务器时可能会导致内存溢出。因此，Session里的信息应该尽量精简。

Session生成后，只要用户继续访问，服务器就会更新Session的最后访问时间，并维护该Session。用户每访问服务器一次，无论是否读写Session，服务器都认为该用户的Session“活跃（active）”了一次。

由于会有越来越多的用户访问服务器，因此Session也会越来越多。为防止内存溢出，服务器会把长时间内没有活跃的Session从内存删除。这个时间就是Session的超时时间。如果超过了超时时间没访问过服务器，Session就自动失效了。

***cookie与session的区别***

- Cookie数据存放在客户端，Session数据放在服务器端
- Cookie的安全性一般，他人可通过分析存放在本地的Cookie并进行Cookie欺骗。在安全性第一的前提下，选择Session更优。重要交互信息比如权限等就要放在Session中，一般的信息记录放Cookie中
- 单个Cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个Cookie，而Session原则上没有限制
- Session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能考虑到减轻服务器性能方面，应当使用Cookie。
- Session 的运行依赖Session ID，而 Session ID 是存在 Cookie 中的，也就是说，如果浏览器禁用了 Cookie，Session 也会失效（但是可以通过其它方式实现，比如在 url 中传递 Session ID，也就是地址重写）



***localStorage***

***什么是localStorage?***

localStorage 是 HTML5 提供的一个 API，他本质上是一个hash（哈希表），是一个存在于浏览器上的 hash（哈希表）。

localStorage生命周期是永久，这意味着除非用户显示在浏览器提供的UI上清除localStorage信息，否则这些信息将永远存在。存放数据大小为一般为5MB,而且它仅在客户端（即浏览器）中保存，不参与和服务器的通信。

***localStorage使用方法***

localStorage和sessionStorage使用时使用相同的API：

```
localStorage.setItem("key","value");	//以“key”为名称存储一个值“value”

localStorage.getItem("key");	//获取名称为“key”的值

localStorage.removeItem("key");	//删除名称为“key”的信息。

localStorage.clear();	//清空localStorage中所有信息
复制代码
```

localStorage 是一个保存于客户端的哈希表，可以用来保存本地的一些数据。并且**不会因为刷新而释放**，所以，**可以使用 localStorage 来实现变量的持久化存储**

**localStorage的特点**

- localStorage 与 HTTP 没有任何关系，所以在HTTP请求时不会带上 localStorage 的值
- 只有相同域名的页面才能互相读取 localStorage，同源策略与 cookie 一致
- 不同的浏览器，对每个域名 localStorage 的最大存储量的规定不一样，超出存储量会被拒绝。最大存5M 超过5M的数据就会丢失。而 Chrome 10MB 左右
- 常用来记录一些不敏感的信息
- localStorage 理论上永久有效，除非用户清理缓存

**sessionStorage**

sessionStorage 的所有性质基本上与 localStorage 一致，唯一的不同区别在于：
sessionStorage 的有效期是页面会话持续，如果页面会话（session）结束（关闭窗口或标签页），sessionStorage 就会消失。而 localStorage 则会一直存在。

**localStorage与sessionStorage的区别**

- localStorage生命周期是永久的，除非被清除，否则永久保存，而sessionStorage仅在当前会话下有效，关闭页面或浏览器后被清除

**相同点可以参考localStorage的特点**
这里再强调一下，这两个存储方式用来存放数据大小一般为5MB，并且仅在客户端（即浏览器）中保存，不参与和服务器的通信。



### 2、OSI网络七层模型

**1. 白话七层模型**



**层次间关系**

![img](https://pic1.zhimg.com/80/v2-03aa1fec40bb99edbe87df33dfe3574c_720w.jpg)

接触过网络知识的人都知道，学习网络知识都会从OSI七层模型开始，无论到哪个阶段，所学习的内容还是在这七层东西里，它看似简单，甚至大多数人概念理论都倒背如流，但还是感觉理解不透彻，那么下面这篇小文章一定可以帮到你。

我们举一个简单的小例子来帮助大家更好的理解：

比如有一个电脑A，经过一些路由器到达主机B，比如A用QQ这个软件，发送一张笑脸给B，A发送的笑脸如何从七层封装到一层。

**应用层：**人机交互，就是人跟这个软件的交互，你点开了这个软件，就是这么简单，没必要去深入理解。

**表示层：**笑脸或者声音或者图片，计算机是不可直接识别的，我们把这些抽象语言（笑脸），转换成编码（C,C++,JAVA），这些语言规矩不同，程序用什么语言写的笑脸就转换对应的编码格式，编码依然是非计算机识别的（人）。其次将我们的编码转换成二进制，只有到了这一步，计算机才可以真正的识别这个数据。一旦变成二进制，我们叫做数据。

**会话层：**A用QQ发送笑脸给好友B，他可能会有好友C,好友D，如何区分好友，就是靠QQ号，QQ号其实就是一个应用程序添加会话地址。（不一定所有程序提供会话层）

上三层负责对数据进行加工处理———数据流层

下四层负责对数据进行传输———传输流层

**传输层：**主机A把笑脸发送给主机B 首先上三层把笑脸变成二进制，到传输层然后给它分段（当然笑脸没必要就是给你理解），接下来每一小段在前面封装一个四层的报头，这个报头就有可能是TCP协议封装的也可能是UDP协议封装的。

TCP/UDP最重要啊的两个：分段和端口号。TCP/UDP相当于两个干活的人，比如说我家厕所堵了（不，别人家的），你找人来疏通，有两个人，一个要100，一个要200。100的只给你弄通，200的给你弄得非常通还清洁。TCP就是200的人，UDP就是100的那位。他两就都干分段和端口号，无非就是TCP干完基本这两个还提供其他服务，说白了两个都是车一个是标配一个是顶配（最基本的轮子发动都有）。

**数据链路层：**主机A访问主机B的时候，首先A必须在数据包填写一个二层报头，因为交换机是一个二层设备，主要识别二层报头，所以主机A再发消息必须要在二层报头标记他自己的MAC还有B的MAC，如果最开始没有B的MAC，A会有ARP行为，先跳过。

A发送的数据帧到了交换机，交换机第一个动作先查看源MAC，看到是A，于是它身上有一个表格，叫MAC地址表，它自学习，就是源MAC是A添加到MAC地址表，表的另一个参数就是会标记这个源MAC（A的MAC）是哪个接口进入的，视作A连接在这个接口。然后才会查看目标MAC，这时候它会查看它的表，有没有B在某一个接口，有没有这个记录，如果有记录它就直接从表里记得这个口发出，这个就是交换机单播行为。如果没有这个记录，不知道B在哪个接口，它会对这个A发出的数据帧进行洪范，除了进入的这个接口以为的所有接口全部复制一份。

**物理层：**不扯协议什么的最容易理解的就是电流，因为无论在复杂的科技，复杂的协议，最后都是转换成电去传输。一样的，把上面所有的数据包转换成连续的电流，最终传送到电脑B，B再按相反的方式一层一层的拆开。

自此完成两次七层（一层封装，一层解封装）。



**2. 标准解释**

**一、是什么**

OSI （Open System Interconnect）模型全称为开放式通信系统互连参考模型，是国际标准化组织 ( ISO ) 提出的一个试图使各种计算机在世界范围内互连为网络的标准框架

`OSI`将计算机网络体系结构划分为七层，每一层实现各自的功能和协议，并完成与相邻层的接口通信。即每一层扮演固定的角色，互不打扰

**二、划分**

`OSI`主要划分了七层，如下图所示：

![图片](https://mmbiz.qpic.cn/mmbiz_png/gH31uF9VIibQuhT3ib0yKlKibfibnff4pLpIqT4WAUpQqGp7XBdVnHZcJLHsLf2PibOWKI6wric6E8qtww3yWNIiabSkw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**应用层**

应用层位于 OSI 参考模型的第七层，其作用是通过应用程序间的交互来完成特定的网络应用

该层协议定义了应用进程之间的交互规则，通过不同的应用层协议为不同的网络应用提供服务。例如域名系统 `DNS`，支持万维网应用的 `HTTP` 协议，电子邮件系统采用的 `SMTP`协议等

在应用层交互的数据单元我们称之为报文

**表示层**

表示层的作用是使通信的应用程序能够解释交换数据的含义，其位于 `OSI`参考模型的第六层，向上为应用层提供服务，向下接收来自会话层的服务

该层提供的服务主要包括数据压缩，数据加密以及数据描述，使应用程序不必担心在各台计算机中表示和存储的内部格式差异

**会话层**

会话层就是负责建立、管理和终止表示层实体之间的通信会话

该层提供了数据交换的定界和同步功能，包括了建立检查点和恢复方案的方法

**传输层**

传输层的主要任务是为两台主机进程之间的通信提供服务，处理数据包错误、数据包次序，以及其他一些关键传输问题

传输层向高层屏蔽了下层数据通信的细节。因此，它是计算机通信体系结构中关键的一层

其中，主要的传输层协议是`TCP`和`UDP`

**网络层**

两台计算机之间传送数据时其通信链路往往不止一条，所传输的信息甚至可能经过很多通信子网

网络层的主要任务就是选择合适的网间路由和交换节点，确保数据按时成功传送

在发送数据时，网络层把传输层产生的报文或用户数据报封装成分组和包，向下传输到数据链路层

在网络层使用的协议是无连接的网际协议（Internet Protocol）和许多路由协议，因此我们通常把该层简单地称为 IP 层

**数据链路层**

数据链路层通常也叫做链路层，在物理层和网络层之间。两台主机之间的数据传输，总是在一段一段的链路上传送的，这就需要使用专门的链路层协议

在两个相邻节点之间传送数据时，数据链路层将网络层交下来的 `IP`数据报组装成帧，在两个相邻节点间的链路上传送帧

每一帧的数据可以分成：报头`head`和数据`data`两部分:

- head 标明数据发送者、接受者、数据类型，如 MAC地址
- data 存储了计算机之间交互的数据

通过控制信息我们可以知道一个帧的起止比特位置，此外，也能使接收端检测出所收到的帧有无差错，如果发现差错，数据链路层能够简单的丢弃掉这个帧，以避免继续占用网络资源

**物理层**

作为`OSI` 参考模型中最低的一层，物理层的作用是实现计算机节点之间比特流的透明传送

该层的主要任务是确定与传输媒体的接口的一些特性（机械特性、电气特性、功能特性，过程特性）

该层主要是和硬件有关，与软件关系不大

**三、传输过程**

数据在各层之间的传输如下图所示：

![图片](https://mmbiz.qpic.cn/mmbiz_png/gH31uF9VIibQuhT3ib0yKlKibfibnff4pLpITJ4ZKhDPWbuf2HPUpCTV8XF8YYTicyYm9y1PwNJQ9kpTwNP8iakCebgA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

- 应用层报文被传送到运输层
- 在最简单的情况下，运输层收取到报文并附上附加信息，该首部将被接收端的运输层使用
- 应用层报文和运输层首部信息一道构成了运输层报文段。附加的信息可能包括：允许接收端运输层向上向适当的应用程序交付报文的信息以及差错检测位信息。该信息让接收端能够判断报文中的比特是否在途中已被改变
- 运输层则向网络层传递该报文段，网络层增加了如源和目的端系统地址等网络层首部信息，生成了网络层数据报
- 网络层数据接下来被传递给链路层，在数据链路层数据包添加发送端 MAC 地址和接收端 MAC 地址后被封装成数据帧
- 在物理层数据帧被封装成比特流，之后通过传输介质传送到对端
- 对端再一步步解开封装，获取到传送的数据





### 3、XSS、CSRF



**推荐阅读**：https://tech.meituan.com/2018/09/27/fe-security.html

https://tech.meituan.com/2018/10/11/fe-security-csrf.html

首先说下最常见的 XSS 漏洞，XSS (Cross Site Script)，跨站脚本攻击，因为缩写和 CSS (Cascading Style Sheets) 重叠，所以只能叫 XSS。

其原理是攻击者向有 XSS 漏洞的网站中输入恶意的 HTML 代码，当其它用户浏览该网站时候，该段 HTML 代码会自动执行，从而达到攻击的目的，如盗取用户的 Cookie，破坏页面结构，重定向到其它网站等。

**XSS 攻击有两大要素：**

1. 攻击者提交恶意代码。
2. 浏览器执行恶意代码。

**一、存储型 XSS**

下面所示的就是 **存储型的XSS攻击**

例如：某论坛的评论功能没有对 XSS 进行过滤，那么我们可以对其进行评论，评论如下：

```html
<script>
while(true) {
    alert('你关不掉我');
}
</script>
```

存储型 XSS 的攻击步骤：

1. 攻击者将恶意代码提交到目标网站的数据库中。
2. 用户打开目标网站时，网站服务端将恶意代码从数据库取出，拼接在 HTML 中返回给浏览器。
3. 用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
4. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

这种攻击常见于带有用户保存数据的网站功能，如论坛发帖、商品评论、用户私信等。

为了**防止持久型 XSS 漏洞**，有以下两点：

**纯前端渲染**

纯前端渲染的过程：

1. 浏览器先加载一个静态 HTML，此 HTML 中不包含任何跟业务相关的数据。
2. 然后浏览器执行 HTML 中的 JavaScript。
3. JavaScript 通过 Ajax 加载业务数据，调用 DOM API 更新到页面上。

**HTML转义**

如果不需要用户输入 HTML，可以直接对用户的输入进行 HTML 转义：

```html
<script>
window.location.href="http://www.xss.com";
</script>
```

经过转义后就成了：

```
&lt;script&gt;window.location.href=&quot;http://www.baidu.com&quot;&lt;/script&gt;
```

**二、DOM 型 XSS**（这是属于前端的安全漏洞）

DOM 型 XSS 的攻击步骤：

1. 攻击者构造出特殊的 URL，其中包含恶意代码。
2. 用户打开带有恶意代码的 URL。
3. 用户浏览器接收到响应后解析执行，前端 JavaScript 取出 URL 中的恶意代码并执行。
4. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作

```html
<input type="text" value="<%= getParameter("keyword") %>">
<button>搜索</button>
<div>
  您搜索的关键词是：<%= getParameter("keyword") %>
</div>
```

然而，在上线后不久，小明就接到了安全组发来的一个神秘链接：

```
http://xxx/search?keyword="><script>alert('XSS');</script>
```

小明带着一种不祥的预感点开了这个链接[请勿模仿，确认安全的链接才能点开]。果然，页面中弹出了写着”XSS”的对话框。

当浏览器请求 `http://xxx/search?keyword="><script>alert('XSS');</script>` 时，服务端会解析出请求参数 `keyword`，得到 `"><script>alert('XSS');</script>`，拼接到 HTML 中返回给浏览器。形成了如下的 HTML：

```html
<input type="text" value=""><script>alert('XSS');</script>">
<button>搜索</button>
<div>
  您搜索的关键词是："><script>alert('XSS');</script>
</div>
```

反射型 XSS 跟DOM型 XSS 的区别是：存储型 XSS 的恶意代码存在数据库里，DOM型 XSS 的恶意代码存在 URL 里。

为了**防止DOM型 XSS 漏洞**：

要使用严谨的前端逻辑，DOM 中的内联事件监听器，如 `location`、`onclick`、`onerror`、`onload`、`onmouseover` 等，`<a>` 标签的 `href` 属性，JavaScript 的 `eval()`、`setTimeout()`、`setInterval()` 等，都能把字符串作为代码运行。如果不可信的数据拼接到字符串中传递给这些 API，很容易产生安全隐患

**三、XSS的防御**

XSS的防御是复杂的。

- **HttpOnly**

HttpOnly最早是由微软提出，并在IE6中实现的，至今已经逐渐成为了一个标准。浏览器将禁止页面的JS访问带有HttpOnly属性的Cookie。

其实严格地说，HttpOnly并非为了对抗XSS——HttpOnly解决的是XSS后的Cookie劫持攻击。HttpOnly现在已经基本支持各种浏览器，但是HttpOnly只是有助于缓解XSS攻击，但仍然需要其他能够解决XSS漏洞的方案。

- **验证码**：防止脚本冒充用户提交危险操作。
- **过滤，**对诸如`<script>、<img>、<a>`等标签进行过滤 
- **编码**，像一些常见的符号，如<>在输入的时候要对其进行转换编码 
- **限制，**对于一些可以预期的输入可以通过限制长度强制截断来进行防御



**CSRF**

跨站请求伪造，CSRF 可以简单理解为：**攻击者盗用了你的身份，以你的名义发送恶意请求，**

![img](https://pic4.zhimg.com/v2-0c63c4193d48b8f42c4a4f53d82330df_r.jpg)

如上图所示：要完成一次 CSRF 攻击，受害者必须完成：

1. **登录受信任网站，并在本地生成 cookie**
2. **在不登出 A 的情况下，访问危险网站 B**

举个简单的例子：

某银行网站 A，它以 GET 请求来完成银行转账的操作，如：

```text
http://www.mybank.com/transfer.php?toBankId=11&money=1000
```

而某危险网站 B，它页面中含有一段 HTML 代码如下：

```html
<img src=http://www.mybank.com/transfer.php?toBankId=11&money=1000>
```

某一天，你登录了银行网站 A，然后又访问了危险网站 B，这时候你突然发现你的银行账号少了 1000 块，原因是银行网站 A 违反了 HTTP 规范，使用 GET 请求更新资源。

B 中的 <img> 以 GET 方式请求第三方资源(指银行网站，这原本是一个合法的请求，但被不法分子利用了)，由于你此前登录银行网站 A 且还未退出，这时候你的浏览器会带上你的银行网站 A 的 Cookie 发出 GET 请求，结果银行服务器收到请求后，认为这是一条合法的更新资源的操作，所以立刻进行转账操作，这样就完成了一次简单的跨站请求伪造。



**一、几种常见的攻击类型**

**GET类型的CSRF**

GET类型的CSRF利用非常简单，只需要一个HTTP请求，一般会这样利用：

```html
 ![](https://awps-assets.meituan.net/mit-x/blog-images-bundle-2018b/ff0cdbee.example/withdraw?amount=10000&for=hacker)
 
```

在受害者访问含有这个img的页面后，浏览器会自动向`http://bank.example/withdraw?account=xiaoming&amount=10000&for=hacker`发出一次HTTP请求。bank.example就会收到包含受害者登录信息的一次跨域请求。

**POST类型的CSRF**

这种类型的CSRF利用起来通常使用的是一个自动提交的表单，如：

```html
 <form action="http://bank.example/withdraw" method=POST>
    <input type="hidden" name="account" value="xiaoming" />
    <input type="hidden" name="amount" value="10000" />
    <input type="hidden" name="for" value="hacker" />
</form>
<script> document.forms[0].submit(); </script> 
```

访问该页面后，表单会自动提交，相当于模拟用户完成了一次POST操作。

POST类型的攻击通常比GET要求更加严格一点，但仍并不复杂。任何个人网站、博客，被黑客上传页面的网站都有可能是发起攻击的来源，后端接口不能将安全寄托在仅允许POST上面。

**CSRF的特点**

- 攻击一般发起在第三方网站，而不是被攻击的网站。被攻击的网站无法防止攻击发生。
- 攻击利用受害者在被攻击网站的登录凭证，**冒充受害者提交操作；而不是直接窃取数据。**
- 整个过程攻击者并不能获取到受害者的登录凭证，仅仅是**“冒用”**。
- 跨站请求可以用各种方式：图片URL、超链接、CORS、Form提交等等。部分请求方式可以直接嵌入在第三方论坛、文章中，难以进行追踪。

CSRF通常是跨域的，因为外域通常更容易被攻击者掌控。但是如果本域下有容易被利用的功能，比如可以发图和链接的论坛和评论区，攻击可以直接在本域下进行，而且这种攻击更加危险。

**二、防护策略**

CSRF通常从第三方网站发起，被攻击的网站无法防止攻击发生，只能通过增强自己网站针对CSRF的防护能力来提升安全性。

上文中讲了CSRF的两个特点：

- CSRF（通常）发生在第三方域名。
- CSRF攻击者不能获取到Cookie等信息，只是使用。

针对这两点，我们可以专门制定防护策略，如下：

- 阻止不明外域的访问
  - 同源检测 
  - Samesite Cookie
- 提交时要求附加本域才能获取的信息
  - **CSRF Token**
  - **双重Cookie验证**

以下我们对各种防护方法做详细说明。

主要了解 **CSRF Token \    双重Cookie验证**

**同源检测**：在HTTP协议中，每一个异步请求都会携带两个Header，用于标记来源域名：Origin Header 、Referer Header

**CSRF Token**：前面讲到CSRF的另一个特征是，攻击者无法直接窃取到用户的信息（Cookie，Header，网站内容等），仅仅是冒用Cookie中的信息。而CSRF攻击之所以能够成功，是因为服务器误把攻击者发送的请求当成了用户自己的请求。那么我们可以要求所有的用户请求都携带一个CSRF攻击者无法获取到的Token。服务器通过校验请求是否携带正确的Token，来把正常的请求和攻击的请求区分开，也可以防范CSRF的攻击。

**双重Cookie验证** 双重提交Cookie。利用CSRF攻击不能获取到用户Cookie的特点，我们可以要求Ajax和表单请求携带一个Cookie中的值。

双重Cookie采用以下流程：

- 在用户访问网站页面时，向请求域名注入一个Cookie，内容为随机字符串（例如`csrfcookie=v8g9e4ksfhw`）。
- 在前端向后端发起请求时，取出Cookie，并添加到URL的参数中（接上例`POST https://www.a.com/comment?csrfcookie=v8g9e4ksfhw`）。
- 后端接口验证Cookie中的字段与URL参数中的字段是否一致，不一致则拒绝。





### 4、**介绍知道的 http 返回的状态码**

**100** 	Continue 继续。客户端应继续其请求 

**101**	 Switching Protocols 切换协议。服务器根据客户端的请求切换协议。只能切换到更 高级的协议，例如，切换到 HTTP 的新版本协议 

**200** 	OK 请求成功。一般用于GET 与POST 请求 

**201** 	Created 已创建。成功请求并创建了新的资源 

**202** 	Accepted 已接受。已经接受请求，但未处理完成 

**203** 	Non-Authoritative Information 非授权信息。请求成功。但返回的 meta 信息不在原始的服务器，而是一个副本 

**204** 	No Content 无内容。服务器成功处理，但未返回内容。在未更新网页的情况下， 可确保浏览器继续显示当前文档

**205** 	Reset Content 重置内容。服务器处理成功，用户终端（例如：浏览器）应重置文档视图。可通过此返回码清除浏览器的表单域 

**206** 	Partial Content 部分内容。服务器成功处理了部分 GET 请求 

**300** 	Multiple Choices 多种选择。请求的资源可包括多个位置，相应可返回一个资源特征与地址的列表用于用户终端（例如：浏览器）选择 

**301**	 Moved Permanently 永久移动。请求的资源已被永久的移动到新 URI，返回信息会包括新的URI，浏览器会自动定向到新 URI。今后任何新的请求都应使用新的 URI 代替 

**302** 	Found 临时移动。与 301 类似。但资源只是临时被移动。客户端应继续使用原有 URI 

**303** 	See Other 查看其它地址。与 301 类似。使用GET 和POST 请求查看 

**304** 	Not Modified 未修改。所请求的资源未修改，服务器返回此状态码时，不会返回 任何资源。客户端通常会缓存访问过的资源，通过提供一个头信息指出客户端希望只返回在指定日期之后修改的资源 

**305** 	Use Proxy 使用代理。所请求的资源必须通过代理访问 

**306** 	Unused 已经被废弃的HTTP 状态码 

**307** 	Temporary Redirect 临时重定向。与 302 类似。使用GET 请求重定向 

**400** 	Bad Request 客户端请求的语法错误，服务器无法理解 

**401** 	Unauthorized 请求要求用户的身份认证 

**402** 	Payment Required 保留，将来使用 

**403** 	Forbidden 服务器理解请求客户端的请求，但是拒绝执行此请求 

**404** 	Not Found 服务器无法根据客户端的请求找到资源（网页）。通过此代码，网站设计人员可设置"您所请求的资源无法找到"的个性页面 

**405** 	Method Not Allowed 客户端请求中的方法被禁止 

**406** 	Not Acceptable 服务器无法根据客户端请求的内容特性完成请求 

**407** 	Proxy Authentication Required请求要求代理的身份认证，与 401 类似，但请求者应当使用代理进行授权 

**408** 	Request Time-out 服务器等待客户端发送的请求时间过长，超时 

**409** 	Conflict 服务器完成客户端的 PUT 请求是可能返回此代码，服务器处理请求时发生了冲突 

**410** 	Gone 客户端请求的资源已经不存在。410 不同于 404，如果资源以前有现在被永久删除了可使用 410 代码，网站设计人员可通过 301 代码指定资源的新位置 

**411** 	Length Required 服务器无法处理客户端发送的不带 Content-Length 的请求信息 

**412** 	Precondition Failed 客户端请求信息的先决条件错误 

**413** 	Request EntityToo Large 由于请求的实体过大，服务器无法处理，因此拒绝请求。为防止客户端的连续请求，服务器可能会关闭连接。如果只是服务器暂时无法处理，则会 包含一个Retry-After 的响应信息 

**414** 	Request-URI Too Large 请求的URI 过长（URI 通常为网址），服务器无法处理 

**415** 	Unsupported Media Type 服务器无法处理请求附带的媒体格式 

**416** 	Requested range not satisfiable客户端请求的范围无效 

**417** 	Expectation Failed 服务器无法满足Expect 的请求头信息 

**500** 	nternal Server Error 服务器内部错误，无法完成请求 

**501** 	Not Implemented 服务器不支持请求的功能，无法完成请求 

**502** 	Bad Gateway 作为网关或者代理工作的服务器尝试执行请求时，从远程服务器接收到了一个无效的响应

**503** 	Service Unavailable 由于超载或系统维护，服务器暂时的无法处理客户端的请求。延时的长度可包含在服务器的 Retry-After 头信息中 

**504** 	Gateway Time-out 充当网关或代理的服务器，未及时从远端服务器获取请求 

**505** 	HTTP Versionnotsupported 服务器不支持请求的HTTP 协议的版本，无法完成处理

### 5、说一下http和https

(1)http 和https 的基本概念 

http: 超文本传输协议，是互联网上应用最为广泛的一种网络协议，是一个客户端和服 务器端请求和应答的标准（TCP），用于从WWW 服务器传输超文本到本地浏览器的传输协议，它可以使浏览器更加高效，使网络传输减少。 

https: 是以安全为目标的HTTP 通道，简单讲是 HTTP 的安全版，即HTTP 下加入SSL层，HTTPS 的安全基础是SSL，因此加密的详细内容就需要SSL。 

https 协议的主要作用是：建立一个信息安全通道，来确保数组的传输，确保网站的真实性。

(2) http 和https 的区别？ 

http 传输的数据都是未加密的，也就是明文的，网景公司设置了 SSL 协议来对http 协 议传输的数据进行加密处理，简单来说 https 协议是由http 和ssl 协议构建的可进行加密传输和身份认证的网络协议，比 http 协议的安全性更高。 

主要的区别如下： 

Https 协议需要ca 证书，费用较高。 

http 是超文本传输协议，信息是明文传输，https 则是具有安全性的ssl 加密传输协议。 

使用不同的链接方式，端口也不同，一般而言，http 协议的端口为 80，https 的端口为 443 

http 的连接很简单，是无状态的；HTTPS 协议是由SSL+HTTP 协议构建的可进行加密传输、身份认证的网络协议，比 http 协议安全。 

(3) https 协议的工作原理 

​	客户端在使用HTTPS 方式与Web 服务器通信时有以下几个步骤，如图所示。 客户使用https url 访问服务器，则要求web 服务器建立ssl 链接。 web 服务器接收到客户端的请求之后，会将网站的证书（证书中包含了公钥），返回或 者说传输给客户端。 

​	客户端和web 服务器端开始协商SSL 链接的安全等级，也就是加密等级。客户端浏览器通过双方协商一致的安全等级，建立会话密钥，然后通过网站的公钥来加 

密会话密钥，并传送给网站。web 服务器通过自己的私钥解密出会话密钥。web 服务器通过会话密钥加密与客户端之间的通信。 

(4)https 协议的优点 

使用HTTPS 协议可认证用户和服务器，确保数据发送到正确的客户机和服务器；HTTPS 协议是由SSL+HTTP 协议构建的可进行加密传输、身份认证的网络协议，要比http 协议安全，可防止数据在传输过程中不被窃取、改变，确保数据的完整性。HTTPS 是现行架构下最安全的解决方案，虽然不是绝对安全，但它大幅增加了中间人攻击的成本。 谷歌曾在 2014 年 8 月份调整搜索引擎算法，并称“比起同等HTTP 网站，采用 HTTPS 加密的网站在搜索结果中的排名将会更高”。 

(5) https 协议的缺点 

https 握手阶段比较费时，会使页面加载时间延长 50%，增加 10%~20%的耗电。 

https 缓存不如http 高效，会增加数据开销。 

SSL 证书也需要钱，功能越强大的证书费用越高。 

SSL 证书需要绑定IP，不能再同一个ip 上绑定多个域名，ipv4 资源支持不了这种消耗





### 6、















# HTML



### 1、



### 2、





### 3、





### 4、



### 5、









# 浏览器







### 1、





### 2、





### 3、



### 4、







### 5、














## 一、项目初始搭建 



下面的项目主要学习 如何去写这些接口，学会这些，其实很多的功能都是类似的

![1623923529(1)](https://z3.ax1x.com/2021/06/17/2xTT9P.png)

![1623923540(1)](https://z3.ax1x.com/2021/06/17/2x7p90.png)

先简单的划分目录解构

[![2x7ZNR.png](https://z3.ax1x.com/2021/06/17/2x7ZNR.png)](https://imgtu.com/i/2x7ZNR)

项目结构优化

[![2x7e41.png](https://z3.ax1x.com/2021/06/17/2x7e41.png)](https://imgtu.com/i/2x7e41)



[![2x7n9x.png](https://z3.ax1x.com/2021/06/17/2x7n9x.png)](https://imgtu.com/i/2x7n9x)



[![2x7VE9.png](https://z3.ax1x.com/2021/06/17/2x7VE9.png)](https://imgtu.com/i/2x7VE9)

但是这个东西怎么拿到呢，这个时候需要借助一个库 dotenv，先下载安装

[![2x7AHJ.png](https://z3.ax1x.com/2021/06/17/2x7AHJ.png)](https://imgtu.com/i/2x7AHJ)





[![2x7u36.png](https://z3.ax1x.com/2021/06/17/2x7u36.png)](https://imgtu.com/i/2x7u36)







## 二、用户注册接口实现



#### 1、postman 简单设置



[![2zGdA0.png](https://z3.ax1x.com/2021/06/17/2zGdA0.png)](https://imgtu.com/i/2zGdA0)



新建环境

[![2zGwNV.png](https://z3.ax1x.com/2021/06/17/2zGwNV.png)](https://imgtu.com/i/2zGwNV)



[![2zGU7q.png](https://z3.ax1x.com/2021/06/17/2zGU7q.png)](https://imgtu.com/i/2zGU7q)



![1623935815(1)](https://z3.ax1x.com/2021/06/17/2z8gFf.png)



[![RSD3rV.png](https://z3.ax1x.com/2021/06/18/RSD3rV.png)](https://imgtu.com/i/RSD3rV)

[![RSD1K0.png](https://z3.ax1x.com/2021/06/18/RSD1K0.png)](https://imgtu.com/i/RSD1K0)



[![RSDQvq.png](https://z3.ax1x.com/2021/06/18/RSDQvq.png)](https://imgtu.com/i/RSDQvq)

#### 2、注册登录简单框架

**下面就展示完全封装好之后的一个简单的 用户登录的时候的代码框架**



[![Rpl9zQ.png](https://z3.ax1x.com/2021/06/18/Rpl9zQ.png)](https://imgtu.com/i/Rpl9zQ)



[![Rpliss.png](https://z3.ax1x.com/2021/06/18/Rpliss.png)](https://imgtu.com/i/Rpliss)



[![RplPMj.png](https://z3.ax1x.com/2021/06/18/RplPMj.png)](https://imgtu.com/i/RplPMj)



[![RplpRg.png](https://z3.ax1x.com/2021/06/18/RplpRg.png)](https://imgtu.com/i/RplpRg)





#### 3、注册信息写入数据库

**用户信息写入到数据库的实现**





接下来就是安装 mysql2 ，让node 连接数据库

[![RPes6s.png](https://z3.ax1x.com/2021/06/19/RPes6s.png)](https://imgtu.com/i/RPes6s)
[![RPeyXn.png](https://z3.ax1x.com/2021/06/19/RPeyXn.png)](https://imgtu.com/i/RPeyXn)

[![RPerlj.png](https://z3.ax1x.com/2021/06/19/RPerlj.png)](https://imgtu.com/i/RPerlj)



[![RPeftU.png](https://z3.ax1x.com/2021/06/19/RPeftU.png)](https://imgtu.com/i/RPeftU)

[![RPeWkT.png](https://z3.ax1x.com/2021/06/19/RPeWkT.png)](https://imgtu.com/i/RPeWkT)





#### 4、用户注册校验

**用户名或密码不能为空**

**用户名不能被注册过**

**也就是一些意外的情况，比如没有传用户名，没有传密码，所以就需要写一个拦截的中间件，在create之前**



[![RFsM40.png](https://z3.ax1x.com/2021/06/20/RFsM40.png)](https://imgtu.com/i/RFsM40)



[![RFslCV.png](https://z3.ax1x.com/2021/06/20/RFslCV.png)](https://imgtu.com/i/RFslCV)



[![RFsKNq.png](https://z3.ax1x.com/2021/06/20/RFsKNq.png)](https://imgtu.com/i/RFsKNq)



[![RFsuEn.png](https://z3.ax1x.com/2021/06/20/RFsuEn.png)](https://imgtu.com/i/RFsuEn)



**下面继续对这些错误的类型做一层封装**

[![RkgHv6.png](https://z3.ax1x.com/2021/06/21/RkgHv6.png)](https://imgtu.com/i/RkgHv6)



[![RkgqKK.png](https://z3.ax1x.com/2021/06/21/RkgqKK.png)](https://imgtu.com/i/RkgqKK)



[![RkgT81.png](https://z3.ax1x.com/2021/06/21/RkgT81.png)](https://imgtu.com/i/RkgT81)







**下面是对另外一种情况，就是要判断用户名不能为空的情况**



[![RkxLE4.png](https://z3.ax1x.com/2021/06/21/RkxLE4.png)](https://imgtu.com/i/RkxLE4)



[![RkxX59.png](https://z3.ax1x.com/2021/06/21/RkxX59.png)](https://imgtu.com/i/RkxX59)



[![RkxbbF.png](https://z3.ax1x.com/2021/06/21/RkxbbF.png)](https://imgtu.com/i/RkxbbF)



[![RkxOUJ.png](https://z3.ax1x.com/2021/06/21/RkxOUJ.png)](https://imgtu.com/i/RkxOUJ)





#### 5、数据库密码加密存储



[![RVefjU.png](https://z3.ax1x.com/2021/06/21/RVefjU.png)](https://imgtu.com/i/RVefjU)

[![RVeWcT.png](https://z3.ax1x.com/2021/06/21/RVeWcT.png)](https://imgtu.com/i/RVeWcT)

[![RVeR3V.png](https://z3.ax1x.com/2021/06/21/RVeR3V.png)](https://imgtu.com/i/RVeR3V)

[![RVe290.png](https://z3.ax1x.com/2021/06/21/RVe290.png)](https://imgtu.com/i/RVe290)











 

## 三、用户登录接口实现

先搭建总体的登录逻辑

之后再去加上中间件，进行 name的校验 以及 password的校验 ，接下来

- 判断用户名和密码是否为空
- 判断用户名是否存在
- 判断密码是否和数据库中的密码一致（加密密码）



#### 1、登录逻辑的简单实现



[![RV2s4e.png](https://z3.ax1x.com/2021/06/22/RV2s4e.png)](https://imgtu.com/i/RV2s4e)



[![RV269H.png](https://z3.ax1x.com/2021/06/22/RV269H.png)](https://imgtu.com/i/RV269H)



[![RV2rND.png](https://z3.ax1x.com/2021/06/22/RV2rND.png)](https://imgtu.com/i/RV2rND)









#### 2、用户名密码校验

[![RVOHGn.png](https://z3.ax1x.com/2021/06/22/RVOHGn.png)](https://imgtu.com/i/RVOHGn)



[![RVOb2q.png](https://z3.ax1x.com/2021/06/22/RVOb2q.png)](https://imgtu.com/i/RVOb2q)



[![RVO7Ps.png](https://z3.ax1x.com/2021/06/22/RVO7Ps.png)](https://imgtu.com/i/RVO7Ps)



[![RVOo5j.png](https://z3.ax1x.com/2021/06/22/RVOo5j.png)](https://imgtu.com/i/RVOo5j)



**下面是一个路由的封装 ，**

[![RZPPWF.png](https://z3.ax1x.com/2021/06/22/RZPPWF.png)](https://imgtu.com/i/RZPPWF)](https://imgtu.com/i/RZPCJU)

[![RZP9iT.png](https://z3.ax1x.com/2021/06/22/RZP9iT.png)](https://imgtu.com/i/RZP9iT)

[![RZPCJU.png](https://z3.ax1x.com/2021/06/22/RZPCJU.png)



#### 3、登录凭证



[![RZW9EV.png](https://z3.ax1x.com/2021/06/22/RZW9EV.png)](https://imgtu.com/i/RZW9EV)



[![RZRzBq.png](https://z3.ax1x.com/2021/06/22/RZRzBq.png)](https://imgtu.com/i/RZRzBq)

[![RZWSH0.png](https://z3.ax1x.com/2021/06/22/RZWSH0.png)](https://imgtu.com/i/RZWSH0)



![1624348082(1)](https://z3.ax1x.com/2021/06/22/RZHuVJ.png)



[![RZHFCq.png](https://z3.ax1x.com/2021/06/22/RZHFCq.png)](https://imgtu.com/i/RZHFCq)
[![RZHP5n.png](https://z3.ax1x.com/2021/06/22/RZHP5n.png)](https://imgtu.com/i/RZHP5n)



**session 的设置**

首先 其实session 是需要借助 cookie 才能完成的，本质上就是 一个cookie



下面是搭建一个简单的服务器，8080端口，配置两个路由，第一个test路由就会种下一个 session

在cookie里面就可以看到

[![Re8p5T.png](https://z3.ax1x.com/2021/06/22/Re8p5T.png)](https://imgtu.com/i/Re8p5T)



[![Re8SaV.png](https://z3.ax1x.com/2021/06/22/Re8SaV.png)](https://imgtu.com/i/Re8SaV)



![1624355497(1)](https://z3.ax1x.com/2021/06/22/ReGZlQ.png)

![1624355688(1)](https://z3.ax1x.com/2021/06/22/ReGjA0.png)

![1624364449(1)](https://z3.ax1x.com/2021/06/22/Re7Jo9.png)





[![Ruh7Ct.png](https://z3.ax1x.com/2021/06/23/Ruh7Ct.png)](https://imgtu.com/i/Ruh7Ct)
[![Ruho4I.png](https://z3.ax1x.com/2021/06/23/Ruho4I.png)](https://imgtu.com/i/Ruho4I)
[![RuhIUA.png](https://z3.ax1x.com/2021/06/23/RuhIUA.png)](https://imgtu.com/i/RuhIUA)







[![RuhH8P.png](https://z3.ax1x.com/2021/06/23/RuhH8P.png)](https://imgtu.com/i/RuhH8P)

[![Ruh5Ed.png](https://z3.ax1x.com/2021/06/23/Ruh5Ed.png)](https://imgtu.com/i/Ruh5Ed)







[![RuhhHH.png](https://z3.ax1x.com/2021/06/23/RuhhHH.png)](https://imgtu.com/i/RuhhHH)



下面是非对称加密，

因为  openssl 在 gitbash 下总是报错，所以代码就没自己实践 ，包括后面的项目也是 使用的对称加密，思路都是一样的



[![RQpFEj.png](https://z3.ax1x.com/2021/06/24/RQpFEj.png)](https://imgtu.com/i/RQpFEj)
[![RQpPbQ.png](https://z3.ax1x.com/2021/06/24/RQpPbQ.png)](https://imgtu.com/i/RQpPbQ)



[![RQpkUs.png](https://z3.ax1x.com/2021/06/24/RQpkUs.png)](https://imgtu.com/i/RQpkUs)



[![RQpA5n.png](https://z3.ax1x.com/2021/06/24/RQpA5n.png)](https://imgtu.com/i/RQpA5n)





**下面是 将登录凭证 ，加入到自己实际项目里面**



[![RQ0IKA.png](https://z3.ax1x.com/2021/06/24/RQ0IKA.png)](https://imgtu.com/i/RQ0IKA)

先在登陆的时候 就去生成一个token，返回到 body上

[![RQ04vd.png](https://z3.ax1x.com/2021/06/24/RQ04vd.png)](https://imgtu.com/i/RQ04vd)



[![RQ0f8e.png](https://z3.ax1x.com/2021/06/24/RQ0f8e.png)](https://imgtu.com/i/RQ0f8e)





[![RQaq5F.png](https://z3.ax1x.com/2021/06/24/RQaq5F.png)](https://imgtu.com/i/RQaq5F)



[![RQaOC4.png](https://z3.ax1x.com/2021/06/24/RQaOC4.png)](https://imgtu.com/i/RQaOC4)





但是每次都要进行手动的复制粘贴，postman可以写脚本，所以可以写一些脚本



[![RQabUU.png](https://z3.ax1x.com/2021/06/24/RQabUU.png)](https://imgtu.com/i/RQabUU)



[![RQaHET.png](https://z3.ax1x.com/2021/06/24/RQaHET.png)](https://imgtu.com/i/RQaHET)













## 四、内容管理系统





实现的功能如下

[![R83SUg.png](https://z3.ax1x.com/2021/06/26/R83SUg.png)](https://imgtu.com/i/R83SUg)
[![R81zVS.png](https://z3.ax1x.com/2021/06/26/R81zVS.png)](https://imgtu.com/i/R81zVS)



#### 1、发布动态简单逻辑实现

[![R3GgWd.png](https://z3.ax1x.com/2021/06/25/R3GgWd.png)](https://imgtu.com/i/R3GgWd)

[![R3GcJH.png](https://z3.ax1x.com/2021/06/25/R3GcJH.png)](https://imgtu.com/i/R3GcJH)

[![R3G6Fe.png](https://z3.ax1x.com/2021/06/25/R3G6Fe.png)](https://imgtu.com/i/R3G6Fe)







#### 2、动态内容插入到数据库



[![R3GOln.png](https://z3.ax1x.com/2021/06/25/R3GOln.png)](https://imgtu.com/i/R3GOln)



[![R3GLSs.png](https://z3.ax1x.com/2021/06/25/R3GLSs.png)](https://imgtu.com/i/R3GLSs)



[![R3GbWj.png](https://z3.ax1x.com/2021/06/25/R3GbWj.png)](https://imgtu.com/i/R3GbWj)





#### 3、查询相应的动态信息



下面是 获取单个用户的评论信息

[![R3d9k6.png](https://z3.ax1x.com/2021/06/25/R3d9k6.png)](https://imgtu.com/i/R3d9k6)



[![R3dPfO.png](https://z3.ax1x.com/2021/06/25/R3dPfO.png)](https://imgtu.com/i/R3dPfO)



[![R3dCtK.png](https://z3.ax1x.com/2021/06/25/R3dCtK.png)](https://imgtu.com/i/R3dCtK)







下面是获取多个用户的评论信息

[![R8MoNj.png](https://z3.ax1x.com/2021/06/26/R8MoNj.png)](https://imgtu.com/i/R8MoNj)



[![R8MT4s.png](https://z3.ax1x.com/2021/06/26/R8MT4s.png)](https://imgtu.com/i/R8MT4s)



[![R8MHCn.png](https://z3.ax1x.com/2021/06/26/R8MHCn.png)](https://imgtu.com/i/R8MHCn)



[![R8MIEQ.png](https://z3.ax1x.com/2021/06/26/R8MIEQ.png)](https://imgtu.com/i/R8MIEQ)



[![R8M4Hg.png](https://z3.ax1x.com/2021/06/26/R8M4Hg.png)](https://imgtu.com/i/R8M4Hg)





#### 4、修改动态内容

基本逻辑就是两点

- 1、用户必须登录
- 2、必须验证用户是否具备这个权限去修改内容

[![RGXTL8.png](https://z3.ax1x.com/2021/06/26/RGXTL8.png)](https://imgtu.com/i/RGXTL8)



[![RGXIQP.png](https://z3.ax1x.com/2021/06/26/RGXIQP.png)](https://imgtu.com/i/RGXIQP)



[![RGXosf.png](https://z3.ax1x.com/2021/06/26/RGXosf.png)](https://imgtu.com/i/RGXosf)



[![RGX4zt.png](https://z3.ax1x.com/2021/06/26/RGX4zt.png)](https://imgtu.com/i/RGX4zt)
[![RGXhRI.png](https://z3.ax1x.com/2021/06/26/RGXhRI.png)](https://imgtu.com/i/RGXhRI)









#### 5、删除动态内容

























## 五、内容评论系统



#### 1、发表新的评论

首先需要拿到是给那个动态进行的评论，也就是 moment_id,下来就是 user_id

[![RtBVoQ.png](https://z3.ax1x.com/2021/06/28/RtBVoQ.png)](https://imgtu.com/i/RtBVoQ)



[![RtBAeS.png](https://z3.ax1x.com/2021/06/28/RtBAeS.png)](https://imgtu.com/i/RtBAeS)



[![RtBEdg.png](https://z3.ax1x.com/2021/06/28/RtBEdg.png)](https://imgtu.com/i/RtBEdg)



[![RtBFL8.png](https://z3.ax1x.com/2021/06/28/RtBFL8.png)](https://imgtu.com/i/RtBFL8)





#### 2、回复评论的评论



这个相比于上面的发表新的评论，只需要增加一个 comment_id，也就是需要知道你是给哪一个评论进行的回复

[![RNBrVS.png](https://z3.ax1x.com/2021/06/28/RNBrVS.png)](https://imgtu.com/i/RNBrVS)



[![RNBBb8.png](https://z3.ax1x.com/2021/06/28/RNBBb8.png)](https://imgtu.com/i/RNBBb8)



[![RNB0Df.png](https://z3.ax1x.com/2021/06/28/RNB0Df.png)](https://imgtu.com/i/RNB0Df)







#### 3、修改评论



[![RaSTpT.png](https://z3.ax1x.com/2021/06/29/RaSTpT.png)](https://imgtu.com/i/RaSTpT)

[![RaS71U.png](https://z3.ax1x.com/2021/06/29/RaS71U.png)](https://imgtu.com/i/RaS71U)

[![RaS5t0.png](https://z3.ax1x.com/2021/06/29/RaS5t0.png)](https://imgtu.com/i/RaS5t0)



[![RaSIhV.png](https://z3.ax1x.com/2021/06/29/RaSIhV.png)](https://imgtu.com/i/RaSIhV)











#### 4、删除评论

​	

[![RaEd0O.png](https://z3.ax1x.com/2021/06/29/RaEd0O.png)](https://imgtu.com/i/RaEd0O)



[![RaEanK.png](https://z3.ax1x.com/2021/06/29/RaEanK.png)](https://imgtu.com/i/RaEanK)











## 六、内容标签管理



#### 1、













#### 2、





## 七、文件管理系统



















## 八、云服务器自动化部署





#### 	1、购买服务器

**1.1 注册阿里云服务器**

云服务器我们可以有很多的选择：阿⾥云、腾讯云、华为云。

⽬前在公司使⽤⽐较多的是阿⾥云；

我⾃⼰之前也⼀直使⽤阿⾥云，也在使⽤腾讯云；

之前华为云也有找我帮忙推⼴他们的活动；

但是在我们的课程中，我选择⽬前使⽤更加⼴泛的阿⾥云来讲解：

我们需要注册阿⾥云账号  	https://aliyun.com/

注册即可，⾮常简单



**1.2.** **购买云服务器**

购买云服务器其实是购买⼀个实例。

1.来到控制台：

https://imgtu.com/i/RfTSnP)


---
description: Why Spring？
---

# - Spring的前世今生

## 1-0. Spring的前世今生

Spring是什么？Spring可以说是Java领域最重要的开发应用框架。

> A software framework is an abstraction in which software providing generic functionality can be selectively changed by additional user-written code, thus providing application-specific software. A software framework provides a standard way to build and deploy applications.
>
> 软件框架是一种抽象形式，它提供了一个具有通用功能的软件，这些功能可以由使用者有选择地更改，从而服务于特定应用的软件。框架提供了一种标准的方式来构建并部署应用。
>
> Wikipedia: Software Framework

在Spring框架出现之前，程序员们或许会使用EJB（Enterprise JavaBean，企业级JavaBean）来开发J2EE应用。但这并不是一件轻松的事。EJB要严格地实现各种不同类型的接口，代码复用性低，配置复杂单调，而且不容易学习，开发效率比较低。

随后Spring出现了，自诞生起，它就快速统治了Java界。让J2EE的开发变得更加简单也许是Spring framework的最大初衷，但现如今Spring已经从Spring framework壮大成了Spring全家桶，极大地解放了生产力，几乎成为了Java 后端开发的代名词。

## 1-1. Java Bean与Spring Bean

那么，我们所说的“使用EJB开发”到底困难在哪里呢？首先得了解下面这个概念——**Bean**。

### JavaBean 

JavaBean是符合一定规范编写的**Java类**，便于封装重用。主要有如下规范： 

* 提供默认的构造函数；
* 对属性提供getter跟setter方法；
* 实现Serializable接口 ；
* 所有属性都应该为private。

> Java语言欠缺属性、事件、多重继承功能。所以，如果要在Java程序中实现一些面向对象编程的常见需求，只能手写大量胶水代码。Java Bean正是编写这套胶水代码的惯用模式或约定。

（对于初学者，可以参考[这篇回答](https://www.zhihu.com/question/19773379/answer/31625054)来理解JavaBean。）

在JSP（Java Server Pages，一种动态网页开发技术）中，程序员可以用JavaBean来封装业务逻辑，保存数据到数据库。JSP直接接受用户的请求，然后通过JavaBean来处理业务。

![JSP Model 2](../.gitbook/assets/image%20%282%29.png)

这样的模型受到了广大Java程序员的欢迎，越来越多的程序员在渴望这样的规范：它能让程序员们只将精力倾注在业务上，至于冗杂的事务管理、安全管理、多线程……都交给容器来处理。于是，J2EE被推出了，又演变成了Java EE，JavaBean也演变成了**EJB**。

（\*与EJB相对应的概念是**POJO**（Plain Ordinary Java Object）。POJO指的是简单的Java对象，实际上就是通指没有使用Entity Beans的普通java对象，是为了避免和EJB混淆所创造的简称。）

然而，程序员们又发现， EJB用起来极为繁琐笨重、性能低下，为了获得所谓的分布式，反而背上了沉重的枷锁。在定义一个简单的无状态SessionBean时，程序员需要写一大堆和业务完全无关的类，还要被迫实现一些根本不应该实现的接口及方法，这是我们所不希望的。

于是，Spring顺应了这种诉求，SpringBean也应运而生。

### SpringBean

Spring Bean是受Spring管理的对象，所有能受Spring容器管理的对象都可以称为Spring Bean。Spring Bean的限制比JavaBean少很多。

对于一个Bean来说，如果它依赖别的Bean，只需要声明即可，Spring容器负责把依赖的bean“注入“进来，是由容器来控制，而非人来控制，这种思想原则就被称为**控制反转（IoC）**，2004年，Martin Flower又给实现这种原则的方式起了个更好的名字——**“依赖注入”（DI）**。

如果一个Bean需要一些像事务、日志、安全这样的通用服务，那么只需要声明，Spring容器在运行时就能够动态地“织入”这些服务， 这被称之为**面向切面编程（AOP）**。

对于使用Spring框架的开发人员来说，主要需要做两件事：

1. 开发Bean
2. 配置Bean

而创建Bean实例，是由Spring来替我们完成的事情。那么，Spring如何知道到底哪些对象是要去进行实例化、装配的呢？这是通过读取配置文件元数据来达成的。配置文件元数据用XML配置、Java注解和Java代码配置来表示，程序员只需要向Spring容器提供配置元数据，Spring容器就能在我们的应用中实例化、装配这些对象，这就达到了降低耦合的效果。

总而言之，但凡是被Spring管理的、由Spring创建的、用于依赖注入的对象，都叫做Bean，Spring创建Bean并完成依赖注入后，把这些Bean统一放在IoC容器中进行管理，由Spring管理其生命周期行为。

**装配Bean是Spring framework的核心功能，是Spring最出彩之处。IoC与AOP是Spring最核心的概念**，在接下来的内容中将对其进行介绍。










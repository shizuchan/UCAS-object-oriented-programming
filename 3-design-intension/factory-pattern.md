# - 工厂模式

纵览了Spring Bean在容器中的生命周期，我们来回顾一下Bean的创建过程：

1. 配置好文件，在配置文件中声明Bean定义，为Bean配置元数据；
2. 由IoC容器来解析元数据。读取并解析配置文件、根据定义生成BeanDefinition配置元数据对象，IoC容器根据BeanDefinition进行实例化、配置及组装Bean。 
3. 实例化IoC容器：由客户端实例化容器，获取需要的Bean。

在这个过程中，IoC容器就像一个工厂一样，当我们需要创建一个Bean对象的时候，只需要开一笔“单子”给工厂（配置好配置文件/注解），完全不用考虑对象是如何被工厂创建出来的，这些事情都交给工厂——IoC 容器来做：创建对象，配置对象，并处理这些对象的整个生命周期，直到它们被完全销毁。 

这就是GoF的23种设计模式中**“**工厂模式**”**的体现。Spring涉及了很多设计模式，下面就来进行介绍。

## 3-1. 工厂模式

来看看GoF给工厂模式的定义：

> Define an interface for creating an object, but let subclasses decide which class to instantiate. Factory Method lets a class defer instantiation to subclasses.”
>
> （在基类中定义一个创建对象的接口，让子类决定实例化哪个类。工厂方法让一个类的实例化延迟到子类中进行。\)

我们想想看Java中对象的创建。想要创建对象，直接new一个是最简单的方式，但Spring没有这么做，这是因为new对象大量出现在业务代码中会带来至少两个问题：

1. 创建对象的细节直接暴露在业务代码中，修改实现细节必须修改相关的大量客户端代码。
2. 直接面向具体类型编程，违反了面向接口编程的原则，系统进行扩展时也不得不进行大量修改。

要使得系统具有的良好的可扩展性以及后期易于维护，必须将”产品的获取“和”产品的使用“解耦。那么，首先就要对客户端代码屏蔽掉创建产品的细节，其次客户端必须面向产品的抽象编程，利用Java的多态这个特性，在运行时才确定具体的产品。这也正是工厂模式的关键。

工厂模式可将Java对象的调用者从被调用者的实现逻辑中分离出来，调用者只需关心被调用者必须满足的规则\(接口\)，而不必关心具体实现过程。

工厂模式细分为简单工厂（Simple Factory）模式、工厂方法（Factory Method）模式和抽象工厂（Abstract Factory）模式，其中运用最多的是**工厂方法**， 它是简单工厂的进一步深化。

在简单工厂里，有一个统一的工厂类来创建所有的对象，简单工厂只有一个实现，没有子类，体现在Spring框架中，就是BeanFactory。BeanFactory容器根据传入的一个唯一的标识来获得Bean对象。根据bean定义，工厂将返回对象的独立实例（prototype模式），或者单个共享实例（替代singleton模式，其实例是工厂范围内的单例），返回哪种类型的实例取决于bean工厂配置。

在简单工厂里，最大的问题是只有一个统一的工厂类，如果我们需要新增类的话就要去修改工厂类，这违背了[**开放-封闭**](https://www.cnblogs.com/gaochundong/p/open_closed_principle.html)原则。而在工厂方法中，不再有一个统一的工厂类来创建所有的产品对象了，而是针对不同的产品提供不同的工厂，系统提供一个与产品等级结构对应的工厂等级结构。

![&#x5DE5;&#x5382;&#x65B9;&#x6CD5;&#x7684;&#x7C7B;&#x56FE;](../.gitbook/assets/image%20%287%29.png)

可以看到参与其中的成员有：

* **Product** \#定义工厂方法所创建的对象的接口。 
* **ConcreteProduct** \#实现Product接口。
* **Creator** \#声明工厂方法，该方法返回一个Product类型的对象。Creator也可以定义一个工厂方法的缺省实现，它返回一个缺省的ConcreteProduct对象。 \#可以调用工厂方法以创建一个Product对象。 
* **ConcreteCreator** \#重定义工厂方法以返回一个ConcreteProduct实例。

简单工厂是一个对象负责所有具体类的实例化，而工厂方法模式是定义一个用于创建对象的接口，由一群子类负责实例化，具有简单工厂所不具备的弹性。

体现在Spring中，我们有FactoryBean：

```java
public interface FactoryBean <T> {
  T getObject() throws java.lang.Exception;
  java.lang.Class<?> getObjectType();
  boolean isSingleton();
}
```

FactoryBean接口由BeanFactory中配置的对象实现，这些对象本身就是用于创建对象的工厂，因此FactoryBean实际上是”工厂的工厂“。如果一个bean实现了这个接口，那么它就是创建对象的工厂bean，即FactoryBean，而不是bean实例本身。

工厂模式同时也是面向对象设计原则中**依赖倒置**（DIP）原则的体现：如果我们直接通过new在高层组件去创建对象，而不委托给工厂（容器），那就意味着高层的组件直接依赖的低层的组件——我们创建的具体的bean。bean改变了，高层组件也要同步改变，低层模块的修改会直接影响到高层模块，这样高层模块很难在不同的环境里复用，因此，高层模块不宜依赖于低层模块，不管是高层还是低层模块，都要依赖于抽象。通过这样的“依赖倒置”，就可以减少了高层模块对低层模块的依赖，提高了高层模块的可复用性。




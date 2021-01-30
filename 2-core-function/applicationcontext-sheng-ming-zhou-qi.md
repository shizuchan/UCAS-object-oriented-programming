# - Bean的生命周期

## 2-3. ApplicationContext的生命周期

既然我们知道，ApplicationContext这个IoC容器是要去管理Bean的，那么它究竟是怎么管理的呢？Spring Bean到底是打哪里来、又到哪里去呢？这就要关注到Bean的生命周期了。

Bean在BeanFactory中的生命周期与在ApplicationContext中的生命周期是不同的，在ApplicationContext容器中，Bean的生命周期如下图所示：

![](../.gitbook/assets/image%20%281%29.png)

容器启动的时候，会先读取bean的xml配置文件，然后将xml中每个bean元素分别转换成BeanDefinition对象，在BeanDefinition对象中有scope属性，它控制着bean的作用域。

1、容器启动后，会对scope为singleton且非延迟加载的bean进行实例化；

2、按照Bean定义信息配置信息，填充注入所有属性；

3、如果Bean实现了BeanNameAware接口，会回调该接口的`setBeanName()`方法，传入该Bean的id，此时Bean就获得了自己在配置文件中的id；

![&#x8BE5;&#x63A5;&#x53E3;&#x7531;Spring&#x63D0;&#x4F9B;&#xFF0C;&#x9ED8;&#x8BA4;&#x5B9E;&#x73B0;&#x3002;&#x5982;&#x679C;&#x4E0D;&#x5B9E;&#x73B0;&#x5C31;&#x65E0;&#x6CD5;&#x8BBE;&#x7F6E;name&#x5C5E;&#x6027;&#x4E86;](../.gitbook/assets/image%20%286%29.png)

![ClassPathXmlApplicationContext&#x5B9E;&#x73B0;&#x4E86;BeanNameAware&#x63A5;&#x53E3;](../.gitbook/assets/image%20%2814%29.png)

4、如果Bean实现了BeanFactoryAware接口，会回调该接口的`setBeanFactory()`方法，传入该Bean的BeanFactory，这样该Bean就获得了自己所在的BeanFactory；

5、如果Bean实现了ApplicationContextAware接口，会回调该接口的`setApplicationContext()`方法，传入该Bean的ApplicationContext，这样该Bean就获得了自己所在的ApplicationContext；

6、如果有Bean实现了BeanPostProcessor接口，就会回调该接口的`postProcessBeforeInitialization()`方法；

7、如果Bean实现了InitializingBean接口，就会回调该接口的`afterPropertiesSet()`方法；

_8、（定制的初始化方法）如果Bean配置了init-method方法，就会执行init-method配置的方法；_

9、如果Bean实现了BeanPostProcessor接口，就会回调该接口的`postProcessAfterInitialization()`方法；

**10、现在Bean准备就绪，可以正式使用该Bean了。**对于scope为singleton的Bean，Spring的ioc容器里会缓存一份该Bean的实例；对于scope为prototype的Bean，每次被调用都会新建一个新的对象，其生命周期就交给调用方管理了，不再由Spring容器进行管理。

11、容器关闭后，如果Bean实现了DisposableBean接口，就会回调该接口的`destroy()`方法；

_12、（定制的销毁方法）如果Bean配置了destroy-method方法，就会执行destroy-method配置的方法_。

**至此，Bean在ApplicationContext中的整个生命周期结束。**

（\*BeanFactory中Bean的生命周期就没有上述5、6、9三个步骤。）




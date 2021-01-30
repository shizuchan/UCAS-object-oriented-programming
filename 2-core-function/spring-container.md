# - Spring容器

## 2-1. IoC容器：BeanFactory/ApplicationContext

前面提到，是容器来负责实例化、配置和装配Spring bean的，Spring的核心就是Spring IoC容器。

Spring为我们提供了两种Bean容器：**BeanFactory**接口（位于`org.springframework.beans`包）和**ApplicationContext**接口（位于`org.springframework.context`包）。

ApplicationContext实际上是BeanFactory的子接口，BeanFactorty接口提供了配置框架及最基本的依赖注入支持，而ApplicationContext完全继承并拓展了BeanFactory，BeanFactory所具有的语义也适用于ApplicationContext，并且ApplicationContext提供了更多面向实际应用的功能。

这两者都是通过Xml配置文件来加载Bean的，主要区别在于BeanFactory是**延迟加载：**

* **BeanFactory ：**通过BeanFactory启动IoC容器时，并不会初始化配置文件中定义的Bean，只有在使用到某个Bean时\(调用`getBean()`\)，才对该Bean进行加载实例化。

* **ApplicationContext ：**配置的Bean如果scope属性是singleton（单例），那么当容器启动时，一次性预先加载所有的Bean。

如果Bean没有完全注入，BeanFactory加载后，会在第一次调用GetBean方法时才抛出异常；而ApplicationContext会在初始化的时候就加载并检查，可以及时检查依赖是否完全注入。

BeanFactory主要是面向Spring框架的基础设施，面对Spring自己；而ApplicationContext主要面向使用Spring的开发者，作为开发者的我们在大多数情况下都会选择使用ApplicationContext。

不过，既然BeanFactory占用更小的内存，因此在某些特殊情形下，例如移动应用内存消耗比较严苛，那么使用更轻量级的BeanFactory是更合理的。而在大多数企业级的应用中，ApplicationContext依然是首选。

## 2-2. Spring Context

下面将选取`spring-context`部分的源码，以ApplicationContext接口为核心，分析其具体功能。

Spring Context主要有以下功能：

* 创建、销毁Bean
* 加载资源文件
* 发布应用程序事件
* 解析消息，支持i18n等

Spring Context源码结构如下，各部分功能已于注释中阐明：

```java
spring-context-5.2.6.RELEASE
		+ META-INF
		- org.springframework
			+ cache						//通用缓存抽象，基于Spring AOP
			- context		  		//Spring Context的核心代码
				+ annotation		//Spring Context相关的一些注解
				+ config				//Spring Context相关配置
				+ event		  		//Spring Context相关事件
				+ expression		//解析expression
				+ index
				+ support				//Spring Context核心实现
				+ weaving				//加载时织入
						ApplicationContext.java
								/*配置应用的核心接口，应用在运行时是只读的*/
						ApplicationContextAware.java
								/*实现ApplicationContextAware的对象可以获得
								  ApplicationContext实例*/
						ApplicationEvent.java
								/*应用事件*/
						ApplicationListener.java
								/*应用事件监听器*/
						ConfigurableApplicationContext.java
								/*提供设置上下文ID，添加监听器，刷新容器等接口*/
						Lifecycle.java
								/*定义start/stop等生命周期接口*/
						LifecycleProcessor.java
						...
			+ instrument.classloading //基于ClassLoader提供运行时织入的功能
			+ scheduling						  //通用任务调度抽象
			+ scripting
			+ stereotype						  //标记分层系统的注解
			+ validation						  //提供数据绑定跟校验相关功能
			...
```

其中，`ApplicationContext`正是Spring Context的焦点，将在下文进行介绍。



 














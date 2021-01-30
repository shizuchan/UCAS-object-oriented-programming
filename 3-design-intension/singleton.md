# - 单例模式

## 3-2. 单例模式

单例模式（Singleton Pattern）是指，确保一个类在任何情况下仅有一个实例，并提供一个访问它的全局访问点（ 自行实例化并向整个程序提供这个实例）。

ApplicationContext采用了单例形式，Spring 中 bean 的默认作用域就是singleton（单例）。 

除了singleton，Spring 中 bean 还有下面几种作用域：

* prototype : 每次请求都会创建一个新的 bean 实例。
* request : 每一次HTTP请求都会产生一个新的bean，该bean仅在当前HTTP request内有效。
* session : 每一次HTTP请求都会产生一个新的 bean，该bean仅在当前 HTTP session 内有效。
* global-session： 全局session作用域，仅仅在基于portlet的web应用中才有意义，Spring5已经没有了。Portlet是能够生成语义代码\(例如：HTML\)片段的小型Java Web插件。它们基于portlet容器，可以像servlet一样处理HTTP请求。但是，与 servlet 不同，每个 portlet 都有不同的会话

由于Spring bean默认是单例模式，当我们试图从Spring容器中取得某个类的实例时，默认情况下，Spring会采用单例模式进行创建。Spring实现单例的方式如下：

```java
<bean id=" " class=" "/>
<bean id=" " class=" " scope="singleton"/> (仅为Spring2.0支持)
<bean id=" " class=" " singleton="true"/>
@Scope(value = "singleton")
```

通过源代码我们可以知道，我们每次创建一个ApplicationContext容器，都会执行`refresh()`方法，这个方法会重新加载配置文件，并且创建的容器对象持有一个所有singleton类型bean的map集合，Spring就通过这个ConcurrentHashMap实现单例注册表，采用单例注册表的方式实现单例模式。（这种方式较为特殊，区别于通常的饿汉、懒汉模式，这里不做介绍。）

用ConcurrentHashMap（线程安全）实现单例注册表的源码如下：

```java
private final Map<String, Object> singletonObjects = new ConcurrentHashMap<String, Object>(64); 
 
public Object getSingleton(String beanName, ObjectFactory<?> singletonFactory) { 
        Assert.notNull(beanName, "'beanName' must not be null"); 
        synchronized (this.singletonObjects) { 
            // 检查缓存中是否存在实例   
            Object singletonObject = this.singletonObjects.get(beanName); 
            if (singletonObject == null) { 
                /*...具体代码省略...*/ 
                try { 
                    singletonObject = singletonFactory.getObject(); 
                } 
                /*...具体代码省略...*/ 
                // 如果实例对象在不存在，我们注册到单例注册表中。 
                addSingleton(beanName, singletonObject); 
            } 
            return (singletonObject != NULL_OBJECT ? singletonObject : null); 
        } 
    } 
    //将对象添加到单例注册表 
    protected void addSingleton(String beanName, Object singletonObject) { 
            synchronized (this.singletonObjects) { 
                this.singletonObjects.put(beanName, (singletonObject != null ? singletonObject : NULL_OBJECT)); 
 
            } 
        } 
} 
```

实现单例的部分源码如下：

```java
public abstract class AbstractBeanFactory implements ConfigurableBeanFactory{  
       /** 
        * 充当了Bean实例的缓存，实现方式和单例注册表相同 
        */  
       private final Map singletonCache=new HashMap();  
       public Object getBean(String name)throws BeansException{  
           return getBean(name,null,null);  
       }  
    ...  
       public Object getBean(String name,Class requiredType,Object[] args)throws BeansException{  
          //对传入的Bean name稍做处理，防止传入的Bean name名有非法字符(或则做转码)  
          String beanName=transformedBeanName(name);  
          Object bean=null;  
          //手工检测单例注册表  
          Object sharedInstance=null;  
          //使用了代码锁定同步块，原理和同步方法相似，但是这种写法效率更高  
          synchronized(this.singletonCache){  
             sharedInstance=this.singletonCache.get(beanName);  
           }  
          if(sharedInstance!=null){  
             ...  
             //返回合适的缓存Bean实例  
             bean=getObjectForSharedInstance(name,sharedInstance);  
          }else{  
            ...  
            //取得Bean的定义  
            RootBeanDefinition mergedBeanDefinition=getMergedBeanDefinition(beanName,false);  
             ...  
            //根据Bean定义判断，此判断依据通常来自于组件配置文件的单例属性开关  
            //<bean id="  " class="  " scope="singleton"/>  
            //如果是单例，做如下处理  
            if(mergedBeanDefinition.isSingleton()){  
               synchronized(this.singletonCache){  
                //再次检测单例注册表  
                 sharedInstance=this.singletonCache.get(beanName);  
                 if(sharedInstance==null){  
                    ...  
                   try {  
                      //真正创建Bean实例  
                      sharedInstance=createBean(beanName,mergedBeanDefinition,args);  
                      //向单例注册表注册Bean实例  
                       addSingleton(beanName,sharedInstance);  
                   }catch (Exception ex) {  
                      ...  
                   }finally{  
                      ...  
                  }  
                 }  
               }  
              bean=getObjectForSharedInstance(name,sharedInstance);  
            }  
           //如果是非单例，即prototpye，每次都要新创建一个Bean实例  
           //<bean id="date" class="java.util.Date" scope="prototype"/>  
           else{  
              bean=createBean(beanName,mergedBeanDefinition,args);  
           }  
    }  
    ...  
       return bean;  
    }  
    
```

ApplicationContext中的单例就是这样实现的。

在实际业务代码中，我们也通常会把工厂类设计为单例模式，单例模式有以下优点：

* 单例模式在内存中只有一个实例，减少了内存开支，当一个对象_需要频繁地创建、销毁时_，而且创建或销毁时的性能又无法优化，单例模式就很有优势。
* 减少了系统的性能开销，当一个对象的产生需要比较多的资源，如读取配置、产生其他依赖对象时，则可以通过在应用启动时直接产生一个单例对象，然后永久驻留内存的方式来解决。
* 避免对资源的多重占用。如避免对同一个资源文件的同时写操作。
* 在系统设置全局的访问点，优化和共享资源访问。



另外， `refresh()`方法是Spring容器启动的核心，关于refresh方法的详细解析，可以参考[这篇文章](https://cloud.tencent.com/developer/article/1497793)。




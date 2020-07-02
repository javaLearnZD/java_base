##Spring相关

###1、ioc
* 控制翻转
* 我们在开发的时候如果需要对其他的类作为引用，往往需要我们自己创建这个引用关系，有些类我们需要时往往需要自己去new一个，这样就会造成我们自己管理的类愈来愈多。
* ioc可以帮助我们构建这种引用关系，新对象的创建和引用关系的构建有spring帮我们完成，我们在使用的时候只需要关注我们自己的业务逻辑即可

###2、aop
* 面向切面编程
* 我们在编写业务代码的时候，往往会碰到一些模板代码，例如日志、安全管控等，如果没有aop，我们在写代码的时候同样需要关注这些其实不算是业务代码的代码，增加我们工作，同时 让各个组件之间紧耦合
* 有了aop，在业务代码中就可以织入一些通用的功能，实现组件之间的解耦合

###3、依赖注入
* 构造器注入
* setter注入
* 接口注入，注解注入

###3、bean的生命周期
* 将bean装载到spring上下文的典型声明周期
* 将bean实例化
* 填充bean的属性
* 调用BeanNameAware的setBeanName方法
* 调用BeanFactoryAware的setBeanFActory的方法
* 调用ApplicationContextAware的setApplicationContext方法
* 调用BeanPostProcessor预初始化方法
* 调用initializingBean的afterPropertiesSet方法
* 调用自定义的初始化方法
* 调用BeanPostProcessor的初始化后方法
* 可以使用bean

*
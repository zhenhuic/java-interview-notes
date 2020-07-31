
# Spring

## 三级缓存

- 一级缓存：singletonObjects，单例池，Spring容器；

- 二级缓存：singletonFactories，存的是bean工厂；

- 三级缓存：earlySingletonObjects，存的是还没有真正变成bean的对象。

一级缓存singletonObjects叫做单例池，里面存的是**bean**，而三级缓存earlySingletonObjects存的是**对象**，只有一个对象完整的经历了bean的生命周期变成一个bean之后才会被存到一级缓存中。

二级缓存是包装bean的工厂类，里面有大量的后置处理器要执行，为了提高性能，所以设计了三级缓存。

**循环引用**：单例对象之间的循环引用，只能是在单例的情况下才会出现，一个原型对象是不会产生循环引用的。

**循环引用过程**：创建A的时候会判断是否有循环依赖（是否正在被创建，beanName会被放到一个list里面），如果有的话会放到二级缓存bean工厂中，然后再注入B对象，B对象也经历一遍bean生命周期，注入A对象，这时候就从二级缓存拿出bean工厂得到A对象，将A对象放入三级缓存中。二级缓存的bean工厂来实现AOP代理（bean工厂是通过后置处理器产生bean）

**Spring容器**：BeanFactory、beanDefinitionMap、三级缓存、Spring后置处理器等等，这些组件组成的Spring容器，而不只是单例池。

## Bean的生命周期

![Bean_Initialization_Steps](知识体系/Bean_Initialization_Steps.jpg)

1. Spring容器初始化，`AbstractApplicationContext.refresh();`;

2. 开始实例化单例类，`AbstractApplicationContext.finishBeanFactoryInitialization(beanFactory);`;

3. 实例化所有单例且非lazy的bean，`beanFactory.preInstantiateSingletons();`;

4. 开始实例化普通的bean，`AbstractBeanFactory.getBean();`，`AbstractBeanFactory.doGetBean();`；

5. `DefaultSingletonBeanRegistry.getSingleton(beanName);`，第一次调用`getSingleton()`返回为空；

6. `AbstractAutowireCapableBeanFactory.createBean(beanName, mbd, args);`；

7. `AbstractAutowireCapableBeanFactory.doCreateBean(beanName, mbdToUse, args);`，真正实例化对象是里面的`AbstractAutowireCapableBeanFactory.createBeanInstance(beanName, mbd, args);`；

8. `AbstractAutowireCapableBeanFactory.populateBean(beanName, mbd, instanceWrapper);`，属性注入；

9. `AbstractAutowireCapableBeanFactory.initializeBean(beanName, exposedObject, mbd);`，Spring回调执行初始化方法初始化bean，这个方法里面`AbstractAutowireCapableBeanFactory.applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);`先执行Spring中的内置处理器`xxxPostProcessor`，也就是`@PostConstruct`注释的方法，然后再`AbstractAutowireCapableBeanFactory.invokeInitMethods(beanName, wrappedBean, mbd);`执行接口（`afterPropertiesSet()`方法）和XML初始化方法（init-method定制的初始化方法）。

## 监听器

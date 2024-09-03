---
title: Spring Joinpoint类注解
date: 2022-07-31 13:33:41
tags:
	- shiro
categories: 笔记
---

# Spring Joinpoint类注解
    joinpoint是AOP 的连接点（一个连接点代表一个被代理的方法）
        静态连接点就是被代理的方法本身，可以直接通过getStaticPart方法调用，动态连接点就是对静态方法之外的增强方法；
        动态连接点从静态连接点的拦截器上获取静态部分，并进行加强，形成动态连接点；
        proceed() 方法得作用是：转到链的下一个拦截器上；
        getStaticPart() 方法的作用是：返回连接点的静态部分；静态部分是一个拥有连接器链的对象；（这个静态方法都要被谁拦截使用，可以通过该方法返回）

    ReflectiveMethodInvocation类是joinpoint的实现类
    
        至此，proceed()方法解析完毕。我们来总结一下。
        首先要明确的一点就是一个连接点代表着一个对象里的一个方法。一个对象里的多个方法，就是多个连接点。每个连接点对象中，都存着一个拦截器链，proceed方法就是遍历拦截器链，如果和连接点所代表的方法一致，则执行MethodInterceptor的invoke方法，进行方法的代理，如果拦截器和代理方法不匹配，则进入下一个拦截器。直到都不匹配，则执行原始方法。

        代理对象的所有方法，都会形成一个连接点对象；

        由此可知，我们定义的方法，最终会封装为相应的MethodInterceptor对象，在连接点的proceed中被调用；而在连接点的拦截器中，已经封装好了连接点所代理方法MethodInterceptor;连接点直接使用拦截器进行方法的调用；

    综合所述，连接点就是AOP中的最小单元，连接点里存放了代理对象的目标类，目标方法，方法拦截器；进行代理的时候，调用拦截器MethodInterceptor的加强方法，执行代理方法；

参考链接：https://blog.csdn.net/qq1309664161/article/details/120159606

# Shiro之@RequiresPermissions注解原理详解
    原理：使用了AOP进行了增强，来判断当前用户是否有该权限标识；
    从proceed()方法进来，然后发送请求，进入断点，加强链上的AopAllianceAnnotationsAuthorizingMethodInterceptor元素就是加强@RequiresPermissions而生成的元素，该类实现了MethodInterceptor 接口，其中invoke方法就是对原对象方法的增强，进入方法，执行到了assertAuthorized()方法，调用其中的getMethodInterceptors()方法获取注解，若有则调用相应的注解Handler去处理相应的逻辑（就是调用Realm中定义的权限获取方法）；
    这样就对注解@RequiresPermissions的方法进行了增强

参考链接：https://blog.csdn.net/qq1309664161/article/details/123181245

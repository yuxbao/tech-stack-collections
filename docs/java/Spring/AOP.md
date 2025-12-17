---
title: AOP
createTime: 2025/12/17 12:52:55
permalink: /java/dkahs88i/
---

### 概念

AOP，也就是⾯向==切⾯==编程，把业务逻辑相同的代码组合到一起，比如统一在方法调用前后==记录日志、权限检查、登录校验、执行事务==等.

其中，这些需要被集中提取的业务逻辑叫做**横切关注点**，类比切一块面包，切在哪里就是我们需要关注的点，而砍下去后形成的结构就是**切面**。

> 在切面中统一处理横切关注点。

#### 切面 @Aspect

切面就是一个个java类，是一个处理逻辑的总体体现，比如定义一个日志处理类，用`@Aspect`注解。

#### 切点 @Pointcut

切点在切面的基础上，表示切面需要在哪里生效。需要定义`切点表达式`，匹配某个`service`层或者指定包下的方法。使用`@Pointcut`注解，比如`execution( com.example.service..*(..))`这样的表达式。

#### 通知 Advice(@Before, @After, @AfterReturning, @AfterThrowing, @Around)

通知是切面中具体执行的代码逻辑也就是方法，不同类型的通知会出现在不同时机。

#### 连接点

连接点是被拦截到的点，Spring中指代被拦截的方法。

#### 织入

把切面逻辑应用到目标对象的过程，Spring AOP使用动态代理实现运行时织入。

#### 目标对象

被切面处理的对象，也就是Spring业务代码中的Service、Controller等类。

### 实现原理

1. 当目标类实现了某一个接口时，使用`JDK动态代理`，也就是`java.lang.reflect.Proxy`。

```java
public interface UserService {
    void save();
}

public class UserServiceImpl implements UserService {
    public void save() {
        System.out.println("save user");
    }
}
```

1. Proxy.newProxyInstance()
2. 生成一个实现了 UserService 的代理类
3. 所有方法调用转发到 InvocationHandler

其具体核心代码如下：
```java
InvocationHandler handler = (proxy, method, args) -> {
    System.out.println("before");
    Object result = method.invoke(target, args);
    System.out.println("after");
    return result;
};

UserService proxy = (UserService) Proxy.newProxyInstance(
        target.getClass().getClassLoader(),
        target.getClass().getInterfaces(),
        handler
);
```

`Proxy Pattern`代理模式，也叫做委托模式，是一种结构型设计模式，通常用于处理两种问题：
1. 控制对基础对象的访问。
2. 在访问基础对象时增加功能

其中，==静态代理在编译期确定，动态代理在运行时确定==。

静态代理依赖接口实现类，代码冗余度高。一般使用动态代理，有JDK动态代理和CGLIB动态代理。

JDK动态代理依赖`反射机制`，在运行时生成代理类的字节码，通过范型参数可以实现扩展。

JDK动态代理过程：
1. 写一个自定义代理工厂`ProxyFactory`，里面方法需要能获取到代理类通过`Proxy.getProxyInstance()`
2. 参数里面需要传入`invocationHandler`实现其中`invoke()`方法，这个方法就可以自定义织入逻辑。


2. 当目标类没有实现接口时，使用`CGLIB动态代理`。

CGLIB(Code Generation Library)是一个基于ASM的字节码生成库，它允许我们在运行时对字节码进行修改和动态生成。CGLIB通过继承方式实现代理。

具体代理过程：
1. 首先实现一个MethodInterceptor，方法调用会被转发到该类的intercept()方法。
2. 然后在需要使用该类的时候，通过CGLIB动态代理获取代理对象。
3. 具体就是使用`Enhancer`类，设置其继承的父类为目标类，并将重写的interceptor目标方法传入为callback。
4. 最后通过create方法创建出代理类。

```java
class MyMethodInterceptor implements MethodInterceptor {
    // 实现MethodInterceptor，方法调用被转发到该类的intercept()方法
    @Override
    public Object intercept(Object obj, Method method, Object []args, MethodProxy proxy) throws Throwable {
        logger.info("love you: " + Arrays.toString(args));
		return proxy.invokeSuper(obj, args);
    }
}

// 在需要使用对象MyClass的时候，通过CDLIB动态代理获取proxy对象。
Enhancer enhancer = new Enhancer();
enhancer.setSuperclass(MyClass.class);
enhancer.setCallback(new MyMethodInterceptor());
MyClass classA = (MyClass)enhancer.create();
System.out.println(classA.sayHello("I love you!"));
```

### 事务

==Transaction是重要的保证编程代码和数一致性的概念的==，Spring提供了两种事务管理模式：==编程式事务==和==声明式事务==。

- 编程式事务就是手动调用事务开始、提交、回滚等操作。
- 声明式事务只需要在需要事务方法上加上`@Trasactional`注解，Spring帮助用户自动处理事务整个周期，标注注解的Bean会被创建代理对象，在方法执行前开启事务，方法正常返回时提交事务，如果方法抛出异常就回滚事务。

> 声明式事务优点是不需要在业务代码中掺杂事务管理代码，缺点是最细粒度只能到方法级别，无法达到代码块级别。

#### 事务概念

对了，这里需要复习一下事务的概念。

>[!note]
>事务是指是程序中一系列严密的逻辑操作，而且所有操作必须全部成功完成，否则在每个操作中所作的所有更改都会被撤消。

==ACID四个特性==
1. 原子性（Atomicity）：操作这些指令时，要么全部执行成功，要么全部不执行。只要其中一个指令执行失败，所有的指令都执行失败，数据进行回滚，回到执行指令前的数据状态。
2. 一致性（Consistency）：事务的执行使数据从一个状态转换为另一个状态，但是对于整个数据的完整性保持稳定。
3. 隔离性（Isolation）：隔离性是当多个用户并发访问数据库时，比如操作同一张表时，数据库为每一个用户开启的事务，不能被其他事务的操作所干扰，多个并发事务之间要相互隔离。
4. 持久性（Durability）：当事务正确完成后，它对于数据的改变是永久性的。

==并发事务会导致的问题=={.info}

- 丢失更新：撤销一个事务时，把其他事务已提交的更新数据覆盖。
- 脏读：在一个事务处理过程里读取了另一个未提交的事务中的数据。
- 幻读：一个事务执行两次查询，第二次结果集包含第一次中没有或某些行已经被删除的数据，造成两次结果不一致，只是另一个事务在这两次查询中间插入或删除了数据造成的。幻读是事务非独立执行时发生的一种现象。
- 不可重复读：一个事务两次读取同一行的数据，结果得到不同状态的结果，中间正好另一个事务更新了该数据，两次结果相异，不可被信任。

==事务隔离级别==

1. Read uncommitted（最低级别，任何情况都无法保证。）
2. Read committed（可避免脏读的发生。）保证一个事务在另一个事务提交后才能读取数据
3. Repeatable read（可避免脏读、不可重复读的发生。） 保证事务开启时，不再允许其他事务修改数据

#### 声明式事务实现原理

通过AOP和代理机制实现，在Spring容器启动，扫描Bean，如果方法有`@Trasactional`注解，Spring不会返回原始Bean实例，而是为==Bean创建代理对象==。

这个代理对象在内部包括了事务处理的逻辑。

==事务拦截器TrascationInterceptor==会根据注解配置，比如**传播行为、隔离级别**等，通过**事务管理器**开启一个新事务，从数据库连接池获取一个连接，关闭其自动提交。

```java
public class TransactionInterceptor implements MethodInterceptor {
    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        // 获取事务属性
        TransactionAttribute txAttr = getTransactionAttribute(invocation.getMethod(),
        invocation.getThis().getClass());
        // 开始事务
        TransactionStatus status = transactionManager.getTransaction(txAttr);
        try {
            // 执⾏⽬标⽅法
            Object retVal = invocation.proceed();
            // 提交事务
            transactionManager.commit(status);
            return retVal;
        } catch (Throwable ex) {
            // 回滚事务
            transactionManager.rollback(status);
            throw ex;
        }
    }
}
```

接着，代理对象会调⽤原始 Bean 实例中真正的业务⽅法，如果业务⽅法顺利执⾏完毕，没有抛出任何异常，那么
拦截器就会通过事务管理器提交事务，将之前的所有数据库操作永久保存。

如果业务⽅法抛出了异常，拦截器会捕获到这个异常，并通过事务管理器回滚事务，将之前的所有数据库操作撤
销。

最后，⽆论事务是提交还是回滚，拦截器都会释放数据库连接。

>[!note]
>`@Trasactional`注解会在非public方法和被嵌套调用事务中失效，比如A调用B用this引用绕过了事务B代理对象，导致事务B不生效。或者catch块捕获异常而不抛出，事务也不会回滚。
>
>此外，Spring 事务默认只对 `RuntimeException`和 `Error` 类型的异常进⾏回滚。如果在代码中抛出的是⼀个
>Checked Exception，是 Exception 的⼦类但不是 RuntimeException 的⼦类，⼜没有通过
>`@Transactional(rollbackFor = Exception.class)`指定事务回归的异常类型，那么事务同样不会回滚。

#### 事务隔离

`TransactionDefination`接口定义了事务隔离级别，描述了一个事务其他并发事务影响的程度。这是SQL标准定义的。

- `DEFAULT`表示使用默认的底层数据库隔离级别。
- 设置级别用`isolation`属性指定。

### 事务传播

当一个事务方法调用另一个事务方法时，方法B的事务是否合并进A，还是开启新事务。

Spring有7种事务传播行为，`REQUIRED`为默认，表示为如果当前存在事务则加入该事务，否则开启一个新事务。

- `REQUIRES_NEW`表示始终开启新事务
- `SUPPORTS`表示当前存在事务则加入，否则以非事务执行
- `NOT_SUPPORTED`表示存在事务则把其挂起，以非事务执行
- `MANDATORY`表示有事务则加入，否则抛出异常
- `NESTED`表示作为父事务的子事务，子事务回滚不影响父事务，但父事务影响子事务，需要数据库支持checkpoint功能

事务传播机制通过`ThreadLocal`实现，因此如果调用方法创建一个新线程，则其中的==执行代码的事务传播会失效==。


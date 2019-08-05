# 第六期 《Effective Java中文版（第3版）》
> 阅读计划：每天 5 条，总共18天。读六天，复习一天，截止 20190823。

## 第1条 用静态工厂方法代替构造器

- 有什么好处？

- 如何做到？

### 五大使用静态工厂方法的优势

1. 允许提供更有意义的名称，更利于理解和使用，而不是固定的构造器名称。
2. 可以避免重复创建对象，节省内存，对预先创建的对象进行缓存，反复使用。
3. 可以指定类的任意子类对象，利用多态，更加灵活
4. 允许在内部实现中包含的类不存在，如 服务提供者接口（SPI），JDBC
5. 允许根据不同参数构造不同的对象，多个构造器方法容易调用错。



### 常见的静态工厂方法惯用名称

- from： 类型转换方法，它只有单个参数，返回该类型的一个相对应的实例

	```java
	Date d = Date.from(instant);
	```

- of：聚合方法，带有多个参数，返回该类型的一个实例，把它们合并起来

	```java
	Set<Rank> faceCards = EnumSet.of(JACK , QUEEN, KING);
	```

- valueOf： from 和 of 更烦琐的一种替代方法

	```java
	Biginteger a = Biginteger.valueOf(Integer.MAX_VALUE);
	```

- instance 或者 get Instance一－返回的实例是通过方法的（如有）参数来 描述

	的，但是不能说与参数具有同样的值

	```java
	StackWalker luke = StackWalker .getinstance(options);
	```

- create 或者 newInstance一一像 instance 或者 getInstance 一样，但 create 或者 newInstance 能够确保每次调用都返回一个新的实例

	```java
	Object newArray = Array.newinstance(classObject, arraylen)
	```

- getType：像 get Instance 一 样，但是在工厂方法处于不同的类中的时候使 用 。 Type 表示工厂方法所返回的对象类型

	```java
	FileStore fs = Files.getFileStore(path);
	```

- newType：像 newInstance 一样，但是在工厂方法处于不同的类中的时候使用,Type 表示工厂方法所返回的对象类型

	```java
	BufferedReader br＝ Files.newBufferedReader(path);
	```

- type：getType 和 newType 的简版

	```java
	List<Complaint> litany ＝Collections.list(legacylitany);
	```

### 小结

构建实例时，优先考虑静态工厂，而不是第一反应定义构造器方法；多考虑静态工厂，多考虑静态工厂，考虑静态工厂。

## 第2条. 遇到多个构造器参数时要考虑使用构建器

- 为什么要这样做？
- 怎么做？

### 建造者模式 Builder Pattern

面对构建一个对象需要大量必备参数和可选参数时，使用构造器方式构建往往混乱难以维护，并且容易出错；利用建造者模式 Builder Pattern，使用 构建器对象 `builder` 来构建对象所需要的参数，必填参数作为构造器参数，可选参数采用赋值方法，结合流式 API 方式通过 `build` 方法构建完整对象.

![](http://ww2.sinaimg.cn/large/006tNc79ly1g5p6do7nljj30me0stmyv.jpg)

### 小结

当类的构造器或者静态工厂中具有多个参数，设计这种类时，优先考虑 Builder 模式，尤其是大多数参数可选或者类型相同的情况下，这样可读性好，有便于维护。

初次之外还可以利用 Lombok 框架的 @Builder 注解，自动生成  Builder 模式的固定代码。

## 第3条 用私有构造器或者枚举类型强化 Singleton 属性

怎么用到枚举类型？

对单例的要求哪有？

作为一个单例，全局唯一，只能有一个实例，并且反序列后仍是同个实例，这一点必须实现 `Serializable` 接口和 `readResolve()` 方法解决。

### 单元素枚举实现单例

单例对象要求防止被重复实例化，以及反序列和序列化后都是同一个实例，优先采用单元素枚举类型的实现方式。

![](http://ww1.sinaimg.cn/large/006tNc79ly1g5p6gjmznoj311s0i2mym.jpg)


### 小结

枚举类型单例也有局限，当单例需要扩展于一个父类时，不能基于 Enum 扩展。

如果需要一个没有父类的单例对象，可以考虑使用枚举方式定义。



## 第4条 通过私有构造器强化不可实例化的能力

- 通用的解决方式是什么样的？
- 针对哪些类

为这个类定义一个私有构造器，它就不能被实例化，并且提供调用的异常提示。

缺陷在于该类就无法被继承使用，因为所有构造器都必须调用父类构造器。

### 小结

通常只提供静态方法和静态字段的类，需要对构造器私有化处理。



## 第5 条 优先考虑依赖注人来引用资源

- 什么场景下使用依赖注入？
- 依赖注入有什么副作用吗？

### 依赖注入 DI

当构建一个实例时，所需的资源通过构造器方式或者特定的赋值方法传入，而不是用静态工具类和单例类使用底层资源。

### 小结

依赖注入虽然灵活，易于测试，但是当依赖过多和关系复杂时就难以维护。



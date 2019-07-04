---
layout: post
title: 人生苦短，我用Mockito
---

没错，我~~胖虎~~今天就是要教大家怎么写单元测试！

## 我的经(keng)历

在书写Swift引擎test时，常常需要和数据库，文件系统等等一些不太稳定的独立系统打交道，比如生成Cube数据，查询Cube数据，读写配置啥的，开始都是生成真实数据，但是很不稳定，test几乎每次都挂。

而且最初写test的思路是完全错误的，当时想当然地认为只要把大致流程走一遍，跑过就行。后来我转变了思路，**即针对每个公共方法进行单独测试，要对其返回值进行验证，必要时要验证其内部的流程是否走对**。

其中，**验证内部流程是否走对的意思是只测试到方法当前一层，调用的底层方法不用管是否成功，也不用管是否发生了效果**，如：读写文件的test，只需验证是否调用了读写的底层IO方法就行，至于文件是否真正做了读写，读写是否成功，本test范围内不关心，那是IO test要验证的内容。

所以如果每个test都遵循这个规范，最后发挥的威力肯定是**巨大**的，test也要解耦的！

借由Mokito+PowerMockito框架，我最终完成了整个test的重构，不得不说Mockito太好用了！

mock这个概念在我实习时才了解到的（记得当时是培训单元测试），当时觉得这个mock好牛逼，那么mock是啥呢？顾名思义，就是模拟对象，返回一个对象，行为都可以自定义。这样可以将不同模块，不同系统之间的依赖分开，单独测试其功能，好处自然多多。

## Mock框架

* EasyMock

  最开始使用的mock框架，但是不怎么好用，写法很繁琐，而且还有很多坑，以及各种难以理解的API和概念，后来我索性自定义一个类来完成mock，要简单粗暴得多，甚至对mock框架嗤之以鼻（没有mock，我能做的更好，有谁会用这种框架啊）当时我是不知道Mockito，不然早上贼船了(~~真香~~)。

* PowerMock

  增强EasyMock和Mockito的框架，可以mock static和private的方法。效果如同其名字，真的很power！

* Mockito

  最初了解到Mockito，网上说API比EasyMock要好用，试用了下发现对我写mock代码没有实质性改善，也就少些几个`xxxTimes()`和`EasyMock.replay(mock1, mock2)`，另外为了团队使用框架的统一，最终采用了EasyMock，毕竟使用多年。后来重构test，有验证方法调用情况的需求，学习对比了下两家mock的用法，立马抛弃了EasyMock，拥抱Mockito。

## Mockito使用

吹了这么久Mockito，该拉出来溜溜了，直接上代码：

```java
// mock一个类
Driver driver = Mockito.mock(Driver.class);
// 定义其行为：when(mock.methodCall()).thenReturn(result);
Mockito.when(driver.connect("url", null)).thenReturn(mock(Connection.class))
        // api支持链式调用，行为也易理解，这里定义其行为先返回对象，后抛出异常
        .thenThrow(SQLException.class)
        // 甚至可以将方法过程开放给用户自定义
        // 其实thenReturn和thenThrow底层都走thenAnswer，单独拎出来更好理解和使用
        .thenAnswer(new Answer<Connection>() {
            @Override
            public Connection answer(InvocationOnMock invocationOnMock) throws Throwable {
                return null;
            }
        });
```
经过深入使用，Mockito比EasyMock好用不只一点两点，让我少写不只一两~~万~~行代码：

* 不用定义行为重复次数，EasyMock 每个exspect后面都要接.xxxTimes()，实在神烦，Mockito的处理要友好的多，thenReturn接受一个返回值数组，数组最后一个元素，作为数组length次及以后所有调用的返回值。

  `andReturn(1).andReturn(2).anyTimes()`和`thenReturn(1, 2)` 若你事先不熟悉他们的用法，你选哪个？

* 不用定义mock对象的所有行为，Mockito会为其定义默认行为（有返回值的返回“零”值，引用即为null，基础类型即为0或false）。

  ```java
  // EasyMock
  EasyMock.exspect(list.get(0)).andReturn(null).anyTimes();
  // ... 省略一万行你单元测试根本用不上的方法定义，“我用不上还要定义？”，你在写test时如是说。
  EasyMock.exspect(list.size()).andReturn(1).anyTimes();
  // Mockito
  Mockito.when(list.get(0)).thenReturn(null);
  Mockito.when(list.size()).thenReturn(1);
  // 朱军选吧
  ```

  **Update**:

  其实EasyMock是支持的，他有三种mock模式：

  - EasyMock.mock：必须用指定参数调用所有预期方法。但不考虑调用次序。调用未预期的方法会失败。
  - EasyMock.createStrictMock：与mock的区别在于必须符合指定调用次序，否则会失败。
  - EasyMock.createNiceMock：与mock的区别在于调用未预期的方法也不会失败，有返回值的方法返回”零“值。

  但我还是要喷，**约定优于配置**，与其让我去摸索，不如给我最简单的best practice！

* 修改一个已有对象（Mockito称之为spy），如有一个现成的非mock出来的ArrayList对象，想修改其size方法的返回值，咋办？

  ```java
  ArrayList<Object> list = Mockito.spy(new ArrayList<Object>());
  Mockito.when(list.size()).thenReturn(100);
  ```

* 对mock void方法的良好支持：

  ```java
  Runnable runnable = EasyMock.mock(Runnable.class);
  
  Runnable runnable = Mockito.mock(Runnable.class);
  // Mockito支持前置定义行为
  Mockito.doThrow(RuntimeException.class).when(runnable).run();
  ```

* Mockito报错友好，更容易找到用法问题。

* 最重要的是，Mockito对verify的支持甩EasyMock几条街（gai），请看：

  ```java
  Runnable runnable = Mockito.mock(Runnable.class);
  runnable.run();
  // 验证对一个mock对象的调用
  Mockito.verify(runnable).run();
  Mockito.verify(runnable， Mockito.times(1)).run();
  // EasyMock有verify吗？好吧，有，好用吗？
  ```

搭配PowerMockito食用，几乎能涵盖所有的mock和verify需求。

# 跟我一起入坑Mockito吧，骚年！

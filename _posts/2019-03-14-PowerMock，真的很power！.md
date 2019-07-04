~~[抛瓦，摸抛瓦！，咳咳，走错片场了。](http://www.devilmaycry5.com/)~~

先引入maven依赖：

```xml
<dependency>
    <groupId>org.powermock</groupId>
    <artifactId>powermock-module-junit4</artifactId>
    <version>${powermock.version}</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.powermock</groupId>
    <artifactId>powermock-api-mockito2</artifactId>
    <version>${powermock.version}</version>
    <scope>test</scope>
</dependency>
```

## Mock static method

最常见的需求：mock static方法，也是采用powerMock的最大理由。

```java
// 用到Powermock必须更换junit runner
@RunWith(PowerMockRunner.class)
// 这一步是对一些类预处理，mock static class需要加入
@PrepareForTest({Static.class})
public class PowerMockitoDemo {
    @Test
    public void f() {
        PowerMockito.mockStatic(Static.class);
        // mock 有返回值的static方法
        PowerMockito.when(Static.f()).thenReturn("you're mocked");
        // mock 无返回值的static方法
        PowerMockito.doThrow(new RuntimeException()).when(Static.class);
        Static.voidF();

        Static.f();
        Static.voidF();
    }
}

class Static {
    static String f() {
        return null;
    }
    static void voidF() {
    }
}
```

## Mock new

mock对象的创建。

```java
@RunWith(PowerMockRunner.class)
// 注意，要预处理new了mock类的类
@PrepareForTest({WhoNew.class})
public class PowerMockitoDemo {
    @Test
    public void f() throws Exception {
        PowerMockito.whenNew(BeNew.class)
                .withNoArguments()
                .thenThrow(InstantiationError.class);

        new WhoNew().f();
    }
}

class BeNew {
}
class WhoNew {
    void f() {
        new BeNew();
    }
}
```

## Partial mock static method

mock部分static方法。

```java
@RunWith(PowerMockRunner.class)
@PrepareForTest({PartialStatic.class})
public class PowerMockitoDemo {
    @Test
    public void f() {
        PowerMockito.spy(PartialStatic.class);
        PowerMockito.when(PartialStatic.f()).thenReturn("you're mocked");
        // 会被替换成mock的结果
        PartialStatic.f();
        // 照常返回-1，如果mockStatic的话，所有方法都会被重置。
        PartialStatic.g();
    }
}

class PartialStatic {
    static String f() {
        return "null";
    }

    static int g() {
        return -1;
    }
}
```

## 经验之谈

- 如果mock过程中因为提前调用到了真实方法而报错，建议用doReturn().when()将调用后置，powermockito的更强力一点，可修改static方法。
- 在Test类上打注解`@PowerMockRunnerDelegate`，可在Powermock Runner的基础上套用别的runner，比如`MockitoJUnitRunner`。

## 不足

- 未见过能mock super的mock框架，有时候的需求不好实现。

更多的可探索官方文档：[Github Wiki](https://github.com/powermock/powermock/wiki)。

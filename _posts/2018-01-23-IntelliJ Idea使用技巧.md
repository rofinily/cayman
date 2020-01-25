# IntelliJ Idea使用技巧

- commit前的操作
- tab应该这么排
- 论idea VCS的bigger
- 定制你的tool bar
- 不想commit所有文件
- 我只是想跑一段小代码
- 代码更改太混乱
- 配置自带的null检查

## commit前的操作

![](新建文件夹/0.png)

git commit界面勾选上这三个
- reformat code 格式化代码
- optimize imports 去掉无用import
- cleanup 去掉一些冗余的东西，例如不必要的向上转型，exception抛出等
提交前就给你整好代码，你只用关心代码逻辑，其他事交给idea做

## tab应该这么排

![](新建文件夹/1.png)

tab竖着排列，还可以按字母序，比之前那个横着排的 看不了几个tab的 找起来还贼麻烦的 ~~不知道高到哪里去了~~

这里设置摆放位置和tab上限

![](新建文件夹/2.png)

## 论idea VCS的bigger
idea的VCS支持 比辣鸡source tree（win）~~不知道高到哪里去了~~
这里加入你的其他git项目

![](新建文件夹/3.png)

然后commit，push，pull，smart checkout，resolve conflicts，cherry pick 各种功能，idea已经把你当傻子了，你只要点点点。

## 定制你的tool bar

打开你的tool bar

![](新建文件夹/4.png)

定制你想要用的按钮，然后点点点吧

![](新建文件夹/5.png)

## 不想commit所有文件？

有时候代码为了能跑起来而做了些改动，但是又不想commit到主库

这时候你可以把这些文件移到另外的change list里面，比如下图这样。点提交的时候，焦点在你要提交的change list上就行

![](新建文件夹/6.png)

## 我只是想跑一段小代码

有时候pull下来代码很多编译错，不是自己的锅，改起来贼麻烦

这时假如我想跑个小段代码，小测试验证下自己的想法，写完run的时候，build不通过，跑不起来

不如这样：

右键recompile xxx.java（**新点的版本已经去掉了**）

![](新建文件夹/6.png)

在run configuration里去掉这个，这个会触发别的文件的build，这个才是元凶啊喂！

![](新建文件夹/7.png)

再run/debug下，美滋滋！

## 代码更改太混乱

通常我们这只有一个名为Default的changelist。

![](新建文件夹/8.png)

随着开发任务的增多，可能我们要修改多个功能，加入多个特性，

但是改着改着代码混在一起了，不好查看，commit也不好提交（每个commit尽量只带一个功能点的更改，后期维护查看log会更清晰）

这时候不如加一个changelist吧！每个changelist都能单独commit，岂不美哉？

![](新建文件夹/9.png)

顺便激活你在修改的changelist，不然你新加的更改要跑到别处去了

![](新建文件夹/10.png)


## 配置自带的null检查

setting里可以设置自定义的注解检查

![](新建文件夹/11.png)

支持Nullable和NotNull检查，这里使用third自带的javax注解（不是很懂为啥要改包名）：

xxx.Nullable和xxx.Nonnull

![](新建文件夹/12.png)

配置之后可警告api调用者和维护者参数是否可为null（当然，只对强迫症有用啦，反正那个高亮我受不了）

![](新建文件夹/13.png)

![](新建文件夹/14.png)

![](新建文件夹/15.png)

除参数外，还可适用于成员变量，局部变量等

![](新建文件夹/16.png)

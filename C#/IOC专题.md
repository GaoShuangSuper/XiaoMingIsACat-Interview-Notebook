<font face="Microsoft.YaHei">

# IOC专题

>希望通过一篇系统的IOC专题文章, 能够帮助学习IOC的你更好地了解和掌握IOC理念.

## 什么是IOC(what)

- IOC(Inverse of Control), 意思是控制反转. 它是一种代码架构设计原则, 一种思想, 并不是某个专属技术.

- 在设计模式的原则中提到过是说, 高层不要依赖于低层. 高层和低层不要依赖细节, 高层和低层都要依赖于抽象. 

- 在代码架构上实现这个原则的一个方式, 就是让代码项目中的高层原本对于低层的细节的依赖, 改变成依赖抽象. 让低层的细节通过抽象的容器`反转`,`注入`到高层中.

- IOC本质上是将高层与低层之间的依赖关系解耦, 这里所谓的高层和低层都是相对而言的. 高层不去依赖低层, 而是去依赖抽象, 即在高层中使用低层的抽象, 但是抽象本身的细节化(即实例化)需要依赖于第三方容器的初始化. 这就是IOC容器的作用. 使得程序能够更易于扩展, 减少修改, 便于测试, 和后期维护.

- IOC内部原理就是DI, 全称是dependency injection, 汉语是依赖注入. 比如一个项目中, 展示层对服务层有依赖, 展示层需要调用服务层的service,就必须要对服务层的代码进行引用, 这里就产生了两者之间的依赖关系. 现在使用IOC的注入原理, 将service通过注入的方式在展示层使用,就实现了依赖反转, 展示层不再依赖服务层, 而是通过"依赖"于第三方的容器.这就使得项目的两个层进行了很好的解耦. 展示层和服务层的开发人员专注于自己负责的部分开发即可.

- IOC内部代码的实现原理基本上属于不停地通过反射, 根据注册好的type去实例化对象. 现阶段基本所有的IOC容器都支持通过容器初始化`构造函数`, `属性`, `方法`.有的容器是通过在这些内容上面加`特性`实现, 有的则根据配置文件的内容实现.

- 依赖注入是实现IOC的一种方法，但不等同于IOC，IOC是一种思想，DI只是一种实现。

- AOP是Aspect Oriented Programming的缩写，即面向切面编程。与面向过程和面向对象的编程方式相比，面向切面编程提供了一种全新的思路，解决了OOP编程过程中的一些痛点。

- IOC的实现原理是利用了反射技术，那么AOP的实现原理是动态代理技术

## 为什么用IOC(why)

>目前有业务需求如下:
小明是某政府企业的IT部门, 目前正准备为当地旅游产业搞一个宾馆预订的系统. 当地的所有宾馆可以在该系统上进行注册之后录入宾馆的信息比如房间数量等. 系统可以统一管理这些宾馆的信息, 游客也可以登录该系统进行宾馆房间的预订. 小明负责这个系统的架构工作.

>小明思考了一下, 这个hotel booking system系统应该是这样的
![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190411164656481-1372640398.png)

``` csharp
    /// <summary>
    /// 将注册到系统的Hotel抽象出IHotel基类
    /// 以后再注册的Hotel都需要实现这个基类的内容
    /// </summary>
    public interface IHotel
    {
        string Name { get; set; }
        int RoomCount { get; set; }
        int LeftRoomCount { get; set; }
        bool Check();
    }

    /// <summary>
    /// 实际注册的hotel类
    /// </summary>
    public class GoldenHotel
    {
        public string Name { get; set; }
        public int RoomCount { get; set; }
        public int LeftRoomCount { get; set; }
        public bool NeedPassPort { get; set; }
        [DInjectionConstruct]
        public GoldenHotel(bool needPassport)
        {
            this.Name = "Golden";
            this.RoomCount = 400;
            this.LeftRoomCount = 400;
            this.NeedPassPort = needPassport;
        }

        public bool Check()
        {
            Console.WriteLine("this is golden hotel for booking check system.");
            return true;
        }
    }
    /// <summary>
    /// 实际注册的hotel类
    /// </summary>
    public class JWHotel : IHotel
    {
        public string Name { get; set; }
        public int RoomCount { get; set; }
        public int LeftRoomCount { get; set; }
        public JWHotel()
        {
            this.Name = "JW";
            this.RoomCount = 400;
            this.LeftRoomCount = 400;
        }

        public bool Check()
        {
            Console.WriteLine("this is jw hotel for booking check system.");
            return true;
        }
    }    
```



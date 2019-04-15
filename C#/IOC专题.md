<font face="Microsoft YaHei">

# IOC专题

>希望通过一篇系统的IOC专题文章, 能够帮助学习IOC的你更好地了解和掌握IOC理念.

## What,什么是IOC

>IOC(Inverse of Control), 意思是控制反转. 它是一种代码架构设计原则, 一种思想, 并不是某个专属技术.

## Why,为什么用IOC

>目前有业务需求如下:
小明是某政府企业的IT部门, 目前正准备为当地旅游产业搞一个宾馆预订的系统. 当地的所有宾馆可以在该系统上进行注册之后录入宾馆的信息比如房间数量等. 系统可以统一管理这些宾馆的信息, 游客也可以登录该系统进行宾馆房间的预订. 小明负责这个系统的架构工作.

![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190411164656481-1372640398.png)

>小明思考了一下, 画出了UML的第一个版本

![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190412111855452-1495337797.png)

>可以看出来这里面的耦合还是很多的, 虽然是分了层, 但是对于以后的扩展来说还是会有很多耦合. 先撸代码为敬.
UI层只负责输入和输出, 责任明确. 以后如果UI从console换成了web, 低层也可以重用.
```Csharp
        static void Main(string[] args)
        {
            try
            {
                string orderStr = "";
                var order = new OrderService().OrderConverter(orderStr);

                var result = new HotelService().CheckOrder(order);

                if (result)
                {
                    Console.WriteLine("order is valied.");
                }
                else
                {
                    Console.WriteLine("order is invalied.");
                }
            }
            catch (Exception e)
            {
                Console.WriteLine(e.ToString());
            }
            Console.ReadKey();
        }
```
> BLL层的代码我就不贴出来了, 感兴趣的话可以去git上去查看. 其结构大致如下.

![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190415152827630-1037915886.png)

>UI层引用了两个service其实都是来自BLL层, 分别是HotelService, OrderService, 显然这两个层之间是有一定的耦合的. 如果是一个大项目的话, 则不能完全的实现前后端分离开发.
而且一般不会直接引用service的实现类, 需要提供一个IService接口后, 使用IOC容器将service注入到UI层 , 供UI层调用. 从而实现代码解耦的目的.

## How , 怎么使用IOC.

>IOC内部原理就是DI, 全称是dependency injection, 依赖注入. 比如一个项目中, 展示层对服务层有依赖, 展示层需要调用服务层的service,就必须要对服务层的代码进行引用, 这里就产生了两者之间的依赖关系. 现在使用IOC的注入原理, 将service通过注入的方式在展示层使用,就实现了依赖反转, 展示层不再依赖服务层, 而是通过"依赖"于第三方的容器.这就使得项目的两个层进行了很好的解耦. 展示层和服务层的开发人员专注于自己负责的部分开发即可.

依据这个概念小明开始重构自己的代码. 

![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190415174112391-1507952499.png)

![](https://img2018.cnblogs.com/blog/1216080/201904/1216080-20190415174513259-916892284.png)

```csharp
    public class RunUI
    {
        //属性注入
        public IOC.IHotelService HotelService { get; set; }
        public IOC.IOrderService OrderService { get; set; }

        //构造函数注入
        //public RunUI(IOC.IHotelService hotelService)
        //{
        //}
        //接口注入
        public void Inject(IOC.IHotelService hotelService)
        {

        }

        public void Run()
        {
            try
            {
                string orderStr = "";
                Common.Order order = OrderService.OrderConverter(orderStr);

                var result = HotelService.CheckOrder(order);

                if (result)
                {
                    Console.WriteLine("order is valied.");
                }
                else
                {
                    Console.WriteLine("order is invalied.");
                }
            }
            catch (Exception e)
            {
                Console.WriteLine(e.ToString());
            }
            Console.ReadKey();
        }
    }
```

```csharp
        private void RunBookingSystem()
        {
            IOC.IHotelService hotelService = new BLL.HotelService();
            IOC.IOrderService orderService = new BLL.OrderService();
            UI.RunUI run = new UI.RunUI();
            run.HotelService = hotelService;
            run.OrderService = orderService;
            run.Run();
        }
```
>此时BLL和UI层是解耦的. 但是这样写不够智能与方便. 但是这已经逐渐开始使用依赖注入的解决方案为项目的代码解耦了. 那么下一步就是手写一个IOC容器来了解容器的工作原理了.

### 手动实现一个简单的IOC容器, 体验一下

[Demo](https://github.com/itdennis/DennisDemos/tree/master/Dennis.Container)

## 结语

- 在设计模式的原则中提到过是说, 高层不要依赖于低层. 高层和低层不要依赖细节, 高层和低层都要依赖于抽象. 在代码架构上实现这个原则的一个方式, 就是让代码项目中的高层原本对于低层的细节的依赖, 改变成依赖抽象. 让低层的细节通过抽象的容器`反转`,`注入`到高层中.
- IOC本质上是将高层与低层之间的依赖关系解耦, 这里所谓的高层和低层都是相对而言的. 高层不去依赖低层, 而是去依赖抽象, 即在高层中使用低层的抽象, 但是抽象本身的细节化(即实例化)需要依赖于第三方容器的初始化. 这就是IOC容器的作用. 使得程序能够更易于扩展, 减少修改, 便于测试, 和后期维护.
- IOC内部代码的实现原理基本上属于不停地通过反射, 根据注册好的type去实例化对象. 现阶段基本所有的IOC容器都支持通过容器初始化`构造函数`, `属性`, `方法`.有的容器是通过在这些内容上面加`特性`实现, 有的则根据配置文件的内容实现.
- 依赖注入是实现IOC的一种方法，但不等同于IOC，IOC是一种思想，DI只是一种实现。
- AOP是Aspect Oriented Programming的缩写，即面向切面编程。与面向过程和面向对象的编程方式相比，面向切面编程提供了一种全新的思路，解决了OOP编程过程中的一些痛点。
- IOC的实现原理是利用了反射技术，那么AOP的实现原理是动态代理技术

>暂时写这么多. 随着我对于IOC的继续深入理解, 会继续分析和解读IOC, 以及其为项目带来的价值.




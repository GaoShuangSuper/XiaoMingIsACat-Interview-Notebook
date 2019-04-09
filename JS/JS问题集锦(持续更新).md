# JS知识点解析

1. null和undefined的区别.

    两者都是表示"空值". null表示这个值没有值, 但是undefined表示这个值根本没有定义. undefined可以理解为系统级的, 出乎意料的, 类似错误的值的空缺, 而null是表示程序级的, 正常的, 在意料之中的值的空缺.如果想将"空缺"赋给变量或者属性, 推荐使用null.

2. js中的数据类型.

    分为两类: 原始类型和对象.
    - 原始类型包括: 数字,字符串, bool值, null, undefined
    - 对象类型,包括:对象, 数组, 函数

3. 全局对象

    包括: 
    - 全局属性, 比如undefined, InFinity, NaN
    - 全局函数, 比如 isNan(), eval()
    - 构造函数, Date(), String()
    - 全局对象, JSON, Math
    - window

4. 对象转换为原始值

    将对象转换为字符串的步骤, 
    - `toString()`, 很多类定义了定制化的toString(). 比如数组.
    - `valueOf()`, 默认是返回对象本身.不是返回一个原始值.
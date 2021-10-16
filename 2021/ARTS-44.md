# ARTS-44

## Algorithm

https://leetcode.com/problems/length-of-last-word/

```java
class Solution {
    public int lengthOfLastWord(String s) {
        int index = s.length() - 1;
        while (s.charAt(index) == ' ') {
            index--;
        }
        int wordLength = 0;
        while (index >= 0 && s.charAt(index) != ' ') {
            wordLength++;
            index--;
        }
        return wordLength;
    }
}
```

## Review

[Introduction to Java Bytecode](https://dzone.com/articles/introduction-to-java-bytecode)

#### JVM Data Types

JVM 定义的数据类型包括`原始类型`和`引用类型`。

**原始类型**

- 数值类型：`byte`(8位),`short`(16位),`int`(32位),`long`(64位),`char`(16位), `float`(32位), `double`(64位)
- 布尔类型：`boolean`
- `returnAddress`: 指针指令

**引用类型**

- 类类型
- 数组类型
- 接口类型

布尔类型在字节码中支持受限，没有指令直接操作布尔值，布尔值会被编译器转换为int，使用int指令操作替代。

#### Stack-Based Architecture

- JVM是基于堆栈的VM架构，而非基于寄存器的架构
- PC寄存器：对于运行在Java程序每一个线程，由PC继勋奇来存储当前指令的地址
- JVM stack：对于每一个线程，都会分配一个栈，来存储局部变量，方法参数和返回值
- Heap：内存被所有线程和存储的对象共享（类的实例和数据），对象的释放由垃圾回收器管理
- 方法区：对于每一个加载的类，方法区存储了方法代码、符号表和常量池
- 数组局部变量，下标从0开始到length-1，长度由编译器来计算；一个局部变量可以保存除了`long`和`double`的任何类型的值，`long`和`double`占用两个局部变量
- 操作数栈用于存储作为指令操作数的中间值，或者将参数传入方法调用

## Tip


## Share

上一篇ARTS定格在2019年10月3日，不由感慨时间过得真快，这两年的代码量和技术书籍的阅读量也是少的可怜。

想要好好吐槽一下工作中的杂七杂八，回头想想还不都是自找的，想要变得优秀，就要持续学习，勇于承担。

这个需要综合能力的时代，技管并重，哪个都不能落下，压力山大。

分享一下《Java经典实例》的配套代码，作者从1995年维护至今，佩服！

- [darwinsys-api](https://github.com/IanDarwin/darwinsys-api)
- [javasrc](https://github.com/IanDarwin/javasrc)



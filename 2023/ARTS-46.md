# ARTS-46

## Algorithm

```java
package org.nocoder.leetcode.solution;

/**
 * AddBinary https://leetcode.com/problems/add-binary/
 * <p>
 * Given two binary strings a and b, return their sum as a binary string.
 * <p>
 * Example 1:
 * <p>
 * Input: a = "11", b = "1"
 * Output: "100"
 * Example 2:
 * <p>
 * Input: a = "1010", b = "1011"
 * Output: "10101"
 * Constraints:
 * <p>
 * 1 <= a.length, b.length <= 104
 * a and b consist only of '0' or '1' characters.
 * Each string does not contain leading zeros except for the zero itself.
 * Related Topics
 * Math
 * String
 * Bit Manipulation
 * Simulation
 *
 * @author yangjl
 * @description
 * @date 2023-05-19 15:31
 **/
public class AddBinary {
    public String addBinary(String a, String b) {
        if (a == null || b == null || a.length() > 10000 || b.length() > 10000)
            throw new RuntimeException("invalid parameters");

        // 两个字符串长度查，在短字符串前面补领0，方便计算
        int d = a.length() - b.length();
        String prefix = "";
        for (int i = 0; i < Math.abs(d); i++)
            prefix += "0";

        // 参数前面补0
        if (d > 0)
            b = prefix + b;
        else
            a = prefix + a;

        String[] aa = a.split("");
        String[] bb = b.split("");
        // 用字符串数组来存储结果，长度用aa和bb的都可以
        String[] res = new String[aa.length];

        // carry表示进位值
        String carry = "0";

        // 从末位开始相加
        for (int i = aa.length - 1; i >= 0; i--) {
            String sum = carry + aa[i] + bb[i];

            if ("000".equals(sum)) {
                res[i] = "0";
                carry = "0";
            }

            if ("001".equals(sum) || "010".equals(sum) || "100".equals(sum)) {
                res[i] = "1";
                carry = "0";
            }

            if ("101".equals(sum) || "110".equals(sum) || "011".equals(sum)) {
                res[i] = "0";
                carry = "1";
            }

            if ("111".equals(sum)) {
                res[i] = "1";
                carry = "1";
            }

            // 计算到数组首位时，依然有进位，需要变长数组，长度+1，首位赋值为1
            if (i == 0 && "1".equals(carry)) {
                // step1. 暂存res到temp
                String[] tem = res;
                // step2. 新建一个比原数组长度+1的数组
                res = new String[aa.length + 1];
                // step3. 首位赋值为1
                res[0] = "1";
                // step4. 把原数组内容复制过来，下标从1开始复制
                for (int i1 = 1; i1 < res.length; i1++)
                    res[i1] = tem[i1 - 1];
            }

        }

        String result = new String();
        for (int i = 0; i < res.length; i++)
            result += res[i];

        return result;
    }
}

```

## Review

以前在完成Review这一项任务的时候，通过皓哥的专栏和引导，发现了很多优秀的国外技术网站，如[medium](https://medium.com/)、[DZone](https://dzone.com)、[javatpoint](https://www.javatpoint.com/)、[IBM Developer](https://www.ibm.com/)等等，阅读高质量的英文文章，可以提高英文水平，也可以学习大佬对技术理解的视角和思维方式。今天阅读的是dzone上一位大佬总结的java11到java17的新特性[What’s New Between Java 11 and Java 17](https://dzone.com/articles/whats-new-between-java-11-and-java-17)，篇幅不长，可以快速了解上一个LTS版本Java11到17的一些新特性。下面对文中提到的新特性作以下简要整理。
1. Text Blocks 
   - 文本块，类似python中的文本块支持换行。
        ```java
        public class TextBlock {
            public static void main(String[] args) {
                String json = """
                        {
                            "name": "Jason",
                            "age": "34",
                            "mail": "yangjinlong86@gmail.com"
                        }
                        """;
                System.out.println(json);
            }
        }
        ```
        输出结果也是带格式的
        ```json
        {
            "name": "Jason",
            "age": "34",
            "mail": "yangjinlong86@gmail.com"
        }
        ```
2. Switch Expressions
    - 冒号改为箭头方式，不需要单独写`break`，代码更简洁
        ```java
        private static void withSwitchExpression(Fruit fruit) {
            switch (fruit) {
                case APPLE, PEAR -> System.out.println("Common fruit");
                case ORANGE, AVOCADO -> System.out.println("Exotic fruit");
                default -> System.out.println("Undefined fruit");
            }
        }
        ```
    - 支持返回值，如果一个case下需要多行代码，使用`yield`关键字指定返回值
        ```java
        private static void withYield(Fruit fruit) {
            String text = switch (fruit) {
                case APPLE, PEAR -> {
                    System.out.println("the given fruit was: " + fruit);
                    yield "Common fruit";
                }
                case ORANGE, AVOCADO -> "Exotic fruit";
                default -> "Undefined fruit";
            };
            System.out.println(text);
        }
        ```
    - `yield`支持旧的 switch 语法，也不需要单独写`break`
        ```java
        private static void oldStyleWithYield(Fruit fruit) {
            System.out.println(switch (fruit) {
                case APPLE, PEAR:
                    yield "Common fruit";
                case ORANGE, AVOCADO:
                    yield "Exotic fruit";
                default:
                    yield "Undefined fruit";
            });
        }
        ```
3. Records
    - Records 用来创建一个不可变的数据类，与传统的类相比，无需我们再写构造器, getters, hashCode, equals 和 toString方法
    - 使用new 创建Record时，如果参数与已经存在的Record相同，那么不会新建的Record副本与原Record是同一个实例。见以下代码最后一行的输出，hashcode是一样的。
        ```java
        private static void basicRecord() {
            record GrapeRecord(Color color, int nbrOfPits) {}
            GrapeRecord grape1 = new GrapeRecord(Color.BLUE, 1);
            GrapeRecord grape2 = new GrapeRecord(Color.WHITE, 2);
            System.out.println("Grape 1 is " + grape1);
            System.out.println("Grape 2 is " + grape2);
            System.out.println("Grape 1 equals grape 2? " + grape1.equals(grape2));
            GrapeRecord grape1Copy = new GrapeRecord(grape1.color(), grape1.nbrOfPits());
            System.out.println("Grape 1 equals its copy? " + grape1.equals(grape1Copy));
            System.out.println(grape1.hashCode() + " " + grape1Copy.hashCode());
        }
        ```
        ```log
        Grape 1 is GrapeRecord[color=java.awt.Color[r=0,g=0,b=255], nbrOfPits=1]
        Grape 2 is GrapeRecord[color=java.awt.Color[r=255,g=255,b=255], nbrOfPits=2]
        Grape 1 equals grape 2? false
        Grape 1 equals its copy? true
        -520085790 -520085790
        ```
    - Record的构造方法中可以增加字段校验逻辑
        ```java
            private static void basicRecordWithValidation() {
                record GrapeRecord(Color color, int nbrOfPits) {
                    GrapeRecord {
                        System.out.println("Parameter color=" + color + ", Field color=" + this.color());
                        System.out.println("Parameter nbrOfPits=" + nbrOfPits + ", Field nbrOfPits=" + this.nbrOfPits());
                        if (color == null) {
                            throw new IllegalArgumentException("Color may not be null");
                        }
                    }
                }
                GrapeRecord grape1 = new GrapeRecord(Color.BLUE, 1);
                System.out.println("Grape 1 is " + grape1);
                GrapeRecord grapeNull = new GrapeRecord(null, 2);
            }
        ```
4. Sealed Classes
    - 使用关键字`sealed`配合`permits`来限定类的继承范围，不被允许的类继承该类会编译错误，提示`'ClassName' is not allowed in the sealed hierarchy`
    - 继承了该类的子类需要声明自身的继承范围，使用关键字`final`、`sealed`和`non-sealed`来限定继承范围
5. Pattern matching for instanceof
    - `instanceof`的类名之后，跟一个变量名，可以省去强转和声明类的步骤
        ```java
        Object o = new GrapeClass(Color.BLUE, 2);
        if (o instanceof GrapeClass grape) {
            System.out.println("This grape has " + grape.getNbrOfPits() + " pits.");
        }
        ```
6. Helpful NullPointerExceptions
    - 当发生空指针异常是，异常信息中直接会告诉你是哪个类的哪个方法导致的
        ```java
        Exception in thread "main" java.lang.NullPointerException: Cannot invoke "com.mydeveloperplanet.myjava17planet.GrapeClass.getColor()" because the return value of "java.util.HashMap.get(Object)" is null
        at com.mydeveloperplanet.myjava17planet.HelpfulNullPointerExceptions.main(HelpfulNullPointerExceptions.java:13)
        ```
7. Compact Number Formatting Support
    - `NumberFormat`类中增加了`format`工厂方法，便于数字类型转换
8. Day Period Support Added
    - `DateTimeFormatter` 中新增了一个`B`模式，可以直接返回时间所属的时段信息
    ```java
    public class DayPeriod {
        public static void main(String[] args) {
            DateTimeFormatter dtf = DateTimeFormatter.ofPattern("B");
            System.out.println(dtf.format(LocalTime.of(8, 0)));
            System.out.println(dtf.format(LocalTime.of(13, 0)));
            System.out.println(dtf.format(LocalTime.of(20, 0)));
            System.out.println(dtf.format(LocalTime.of(23, 0)));
            System.out.println(dtf.format(LocalTime.of(0, 0)));
        }
    }
    ```
    
    ```log
    上午
    下午
    晚上
    晚上
    午夜
    ```
    默认返回值是当前时区，也可以指定Locale，例如：
    ```java
    dtf = DateTimeFormatter.ofPattern("B").withLocale(Locale.forLanguageTag("NL"));
    ```

9. Stream.toList()
    - 旧版本中，如果要把Stream转换成List，需要调用`collect(Collectors.toList)`，Java 17 中，`Stream`增加了`toList()`方法，可以直接转换为`List`
        ```java
        private static void oldStyle() {
            Stream<String> stringStream = Stream.of("a", "b", "c");
            List<String> stringList =  stringStream.collect(Collectors.toList());
            for(String s : stringList) {
                System.out.println(s);
            }
        }
        
        private static void streamToList() {
            Stream<String> stringStream = Stream.of("a", "b", "c");
            List<String> stringList =  stringStream.toList();
            for(String s : stringList) {
                System.out.println(s);
            }
        }
        ```

## Tip

使用RabbitMQ时，如果设置了自动ack，再自行手动ack的话，会偶发性的导致消息丢失。

**问题现象**

A服务将数据通过MQ发送到B服务，期间出现消息丢失情况。

**排查过程**

1、A服务发送成功后记录了日志，消息是成功发送出去了。
2、B服务日志打印不规范，输出日志为null，经核实代码，推断`message`为空，导致`RuntimeException`。
3、查看RabbitMQ日志，发现异常信息如下：
```log
2023-05-29 23:52:01.502 [error] <0.20990.12> Channel error on connection <0.2510.0> (10.42.0.1:42792 -> 10.42.0.5:5672, vhost: '/', user: 'admin'), channel 1150:
operation basic.ack caused a channel exception precondition_failed: unknown delivery tag 1
```

**问题原因**

到此问题基本明晰了，生产环境的配置文件没有将rabbitmq的ack设置为手动，默认值为`none`，是自动确认。代码中使用`channel.basicAck()`又进行了手动确认，这样的重复确认操作会导致**偶发性**消息丢失情况。

rabbitmq 为每一个channel维护了一个delivery tag的计数器，采用正向自增，新消息投递时自增，当消息响应时自减；在连续收发的场景中，由于消息发送的间隔较短，部分消息因 consumer的重复确认被rabbitmq 当做已处理而丢弃。

**解决办法**

取消自动确认，配置手动确认。
```yml
spring:
  rabbitmq:
    host: ${rabbitmqHost}
    port: ${rabbitmqPort}
    username: ${rabbitmqUsername}
    password: ${rabbitmqPassword}
    virtual-host: /
    listener:
      simple:
        retry:
          enabled: true
          max-attempts: 24
          initial-interval: 3600000
        acknowledge-mode: manual
```


## Share

- MegaEase纪念陈皓的[网站](https://memorial.megaease.cn/)
- IBM Developer 中文网站已经在2021年3月15日关停，附上一个github上的[文章备份仓库](https://github.com/nocdr/ibm_bak)。


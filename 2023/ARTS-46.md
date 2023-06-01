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
1. 文本块，类似python中的文本块支持换行。
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
2. 


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


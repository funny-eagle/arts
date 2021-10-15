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


## Tip


## Share

上一篇ARTS定格在2019年10月3日，不由感慨时间过得真快，这两年的代码量和技术书籍的阅读量也是少的可怜。

想要好好吐槽一下工作中的杂七杂八，回头想想还不都是自找的，想要变得优秀，就要持续学习，勇于承担。

这个需要综合能力的时代，技管并重，哪个都不能落下，压力山大。

分享一下《Java经典实例》的配套代码，作者从1995年维护至今，佩服！

- [darwinsys-api](https://github.com/IanDarwin/darwinsys-api)
- [javasrc](https://github.com/IanDarwin/javasrc)



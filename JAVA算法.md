# JAVA 
## JAVA做题时的输入问题
一般使用JAVA中的Scanner类来获取键盘输入，使用hasNext()或hasNextLine()来判断是否结束输入，用next()或next[类型]()来获取下一个值
```JAVA
  import java.util.Scanner;
  Scanner scanner = new Scanner(System.in);
  //一般再用 scanner.hasNextLine() 来判断是否继续获取输入
  //想获取键盘输入的任意值，用scanner.next()，获取整数值用scanner.nextInt(),
  //获取浮点数，字节用nextFloat()，nextByte()，其他类似
```
- next() 与 nextLine() 区别</br>

**next():**</br>
- 一定要读取到有效字符后才可以结束输入。
- 对输入有效字符之前遇到的空白，next() 方法会自动将其去掉。
- 只有输入有效字符后才将其后面输入的空白作为分隔符或者结束符。
- **不能得到带有空格的字符串。**
**nextLine()：**</br>
- 以Enter为结束符,也就是说 nextLine()方法返回的是输入回车之前的所有字符。
- 可以获得空白。

## StringBuffer和StringBuilder区别
这两者可以任意修改且不产生新的对象，但StringBuffer是线程安全的，StringBuilder不是线程安全的</br>
StringBuilder的速度快于StringBuffer

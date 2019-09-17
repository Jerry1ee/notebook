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

## 链表反转
依次把节点指向前置节点，最后输出最后一个前置节点，注意这时要把后置节点存起来，因为当前节点指向前置节点时，会将后置节点丢失
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
 
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode cur=head;      //当前节点
        ListNode nextNode=null;//后置节点
        ListNode preNode=null;//前置节点
        while(cur!=null)
        {
          nextNode=cur.next;  //先把后置节点存起来 （例如当前节点是1，后置节点是2）
          cur.next=preNode;   //让当前节点的下一个指向前置节点（1指向空，此时前置节点为空）
          preNode=cur;      //把前置节点指向当前节点（此时1变为了前置节点，取代之前的空）
          cur=nextNode;     //把当前节点指向后置节点（此时2变为了当前节点，取代了1，这时2的前置节点为1，后置节点为3）
        }
        return preNode;
    }
}
```

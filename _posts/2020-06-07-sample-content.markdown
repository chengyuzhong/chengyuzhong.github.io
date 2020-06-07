---
layout: default
title:  "《算法》- 队列和栈"
date:   2020-06-07 20:00:00
categories: main
---

**1、队列：** 先进先出（FIFO），例如超市的收银台、排队买票的顾客。在Java中，它和List的区别在于，List可以在任意位置添加和删除元素，而Queue只有两个操作：
* 把元素添加到队列末尾；
* 从队列头部取出元素。

**2、栈：** 下压栈，后进先出（LIFO），例如你办公桌上的一叠信件，新信件来时将它们放在最上面(push方法)，当阅读时从上到下取件(pop方法)。

**3、双栈算术表达式求值：** 
 例如计算`(1+((2+3)*(4*5)))`的值：用两个栈，一个保存运算符（运算符栈），一个保存数字（操作数栈）。
>从左到右逐个将实体送入栈处理：
1、遇到数字时，将数字压入操作数栈，遇到运算法时，压入运算符栈；
2、遇到左括号时忽略；
3、遇到右括号，弹出一个运算符，弹出所需数量的数字，并将运算符和数字的运算结果压入操作数栈。

<img src="https://img2020.cnblogs.com/blog/1110897/202006/1110897-20200606215815444-1663346954.png" width = "450" alt="算法演示"/>

双栈算术表达式求值算法(为了代码简洁未考虑异常)：
```
import java.util.Stack;

public class EvaluateTest {
    public static void main(String[] args) {
        // 需要计算的表达式
        String str = "(1+((2+3)*(4*5)))".trim();

        // 运算符栈和操作数栈
        Stack<String> ops = new Stack<>();
        Stack<Double> vals = new Stack<>();

        for(int i=0;i < str.length();i++) {
            String s = String.valueOf(str.charAt(i));
            if(s.equals("(")){
                // 左括号时忽略
            }else if(s.equals("+") || s.equals("-") || s.equals("*") || s.equals("/") || s.equals("sqrt")){
                // 运算符时压运算符栈
                ops.push(s);
            }else if(s.equals(")")){
                // 右括号时，将两个栈都pop，再计算、压栈
                String op = ops.pop();
                Double v = vals.pop();
                if(op.equals("+")){
                    v = vals.pop() + v;
                }else if(op.equals("-")){
                    v = vals.pop() - v;
                }if(op.equals("*")){
                    v = vals.pop() * v;
                }if(op.equals("/")){
                    v = vals.pop() / v;
                }if(op.equals("sqrt")){
                    v = Math.sqrt(v);
                }
                vals.push(v);
            }else{
                // 最后是数字时，转为double压入操作数栈
                vals.push(Double.valueOf(s));
            }
        }
        System.out.println("最终运算结果：" + vals.pop());
    }
}
```

**4、链表**
链表是一种递归的数据结构，它可以为空（null），可以是指向一个节点（node）的引用。该节点包含一个元素（数据域，储存节点含有的信息）和一个指向另一条链表或节点的引用（引用域）。

>链表的特点：
>- 插入和删除元素方便；
>- 查找数据时效率低，访问某个位置的数据要从第一个节点开始访问，根据第一个节点保存的下一个节点的地址找到第二个节点，以此类推；

可以看完下面的流程再来理解它的特点。这里主要介绍单向链表：
```
// 一个节点
public class Node {
    public Object item;
    public Node next;
}
```

伪代码构造一条链表：
![](https://img2020.cnblogs.com/blog/1110897/202006/1110897-20200606215950325-2066039066.png)

创建好链表后，在表头插入一个节点很容易，如下图，如果在表头插入字符串"not"，先将first保存在oldfirst中，然后将一个新节点赋给first，并将它的item元素设为not，next设为oldfirst。
![](https://img2020.cnblogs.com/blog/1110897/202006/1110897-20200606221936496-915430739.png)

在表头删除一个节点，将first指向first.next即可。曾经的第一个节点对象变成了一个孤儿，Java的内存管理最终将回收它所占用的内存：
![](https://img2020.cnblogs.com/blog/1110897/202006/1110897-20200606222700155-604987885.png)
> 请注意：当链表中只有一个节点时，它既是首节点又是尾节点，另外注意链表为空的情况。

那么在表尾插入节点可以表示为：
![](https://img2020.cnblogs.com/blog/1110897/202006/1110897-20200606222936370-1071922751.png)

以前链表操作只需要几行赋值代码，所需时间和链表的长度无关。但如果需要删除表尾节点，就要遍历整条链表并找出指向last节点的节点，这样所需的时间和链表的长度成正比。
要想实现任意插入和删除操作，可以使用**双向链表**，这里不作介绍。

**5、遍历链表** 我们知道遍历数组可以用`for(int i = 0; i < N; i++){...}`；那么遍历链表也有一个对应方式:
```
for (Node x = first; x != null; x = x.next) {
   // 处理 x.item
}
```

**6、堆栈的链表实现** 
直接上代码：
```
import java.util.Iterator;
import java.util.NoSuchElementException;

public class Stack<Item> implements Iterable<Item> {
    private Node<Item> first;     // 栈顶（最近添加的元素）
    private int n;                // 元素数量

    private static class Node<Item> {
        private Item item;
        private Node<Item> next;// 定义了节点的嵌套类
    }

    /**
     * Initializes an empty stack.
     */
    public Stack() {
        first = null;
        n = 0;
    }

    /**
     * 当first==null或者n==0时，栈是空的
     */
    public boolean isEmpty() {
        return first == null;
    }
    
    public int size() {
        return n;
    }

    /**
     * 向栈顶添加元素
     */
    public void push(Item item) {
        Node<Item> oldfirst = first;
        first = new Node<Item>();
        first.item = item;
        first.next = oldfirst;
        n++;
    }

    /**
     * 从栈顶删除元素
     */
    public Item pop() {
        if (isEmpty()) throw new NoSuchElementException("Stack underflow");
        Item item = first.item;
        first = first.next;
        n--;
        return item;
    }


    /**
     * 只取值，不删除
     */
    public Item peek() {
        if (isEmpty()) throw new NoSuchElementException("Stack underflow");
        return first.item;
    }

    /**
     * 按照LIFO的顺序，返回一个迭代器可以迭代此类
     */
    public Iterator<Item> iterator() {
        return new LinkedIterator(first);
    }
    
    private class LinkedIterator implements Iterator<Item> {
        private Node<Item> current;

        public LinkedIterator(Node<Item> first) {
            current = first;
        }
        public boolean hasNext() {
            return current != null;
        }
        public void remove() {
            throw new UnsupportedOperationException();
        }
        public Item next() {
            if (!hasNext()) throw new NoSuchElementException();
            Item item = current.item;
            current = current.next;
            return item;
        }
    }
}
```

**7、队列的链表实现**  
Queue的实现使用的数据结构和Stack类相同，都是链表，但它实现了不同的添加和删除算法，这也是FIFO和LIFO的区别所在。
```
import java.util.Iterator;
import java.util.NoSuchElementException;

public class Queue<Item> implements Iterable<Item> {
    private Node<Item> first;    // 指向最早添加的节点的引用
    private Node<Item> last;     // 队尾，最近添加
    private int n;

    private static class Node<Item> {
        private Item item;
        private Node<Item> next;
    }

    public Queue() {
        first = null;
        last  = null;
        n = 0;
    }

    public boolean isEmpty() {
        return first == null;
    }

    public int size() {
        return n;
    }

    /**
     * 向表尾添加元素
     */
    public void enqueue(Item item) {
        Node<Item> oldlast = last;
        last = new Node<Item>();
        last.item = item;
        last.next = null;
        if (isEmpty()){
            first = last;
        }else{
            oldlast.next = last;
        }
        n++;
    }

    /**
     * 从表头删除元素
     */
    public Item dequeue() {
        if (isEmpty()) throw new NoSuchElementException("Queue underflow");
        Item item = first.item;
        first = first.next;
        n--;
        if (isEmpty()) last = null;
        return item;
    }

    /**
     * 从表头获取元素，不删除
     */
    public Item peek() {
        if (isEmpty()) throw new NoSuchElementException("Queue underflow");
        return first.item;
    }

    public Iterator<Item> iterator()  {
        return new LinkedIterator(first);
    }

    private class LinkedIterator implements Iterator<Item> {
        private Node<Item> current;

        public LinkedIterator(Node<Item> first) {
            current = first;
        }
        public boolean hasNext()  { return current != null;                     }
        public void remove()      { throw new UnsupportedOperationException();  }

        public Item next() {
            if (!hasNext()) throw new NoSuchElementException();
            Item item = current.item;
            current = current.next;
            return item;
        }
    }
}
```



相关文献：
<a href="https://algs4.cs.princeton.edu/13stacks/" target="_blank">Bags, Queues, and Stacks</a>

# Java SE - Collection/Map

## Collection

### List

#### ArrayList



#### LinkedList



- 元素有序
- Vector：矢量列表（ArrayList的早期实现，线程安全），数组底层，随机查询快，增删慢，同步，效率低
- Stack：继承Vector，先进后出。
- ArrayList：数组底层，随机查询快，增删慢，不同步，效率高，初始容量是10，扩容50%
- List 不能强转为ArrayList，但是可以使用ArrayList的构造方法转化

### Set

- 元素无序不重复，可以为null
- hashCode返回元素相加结果
- HashSet：无序、非同步、可为null
- LinkedHashSet：根据hashcode值觉得元素存储位置，按元素添加顺序访问。链表保证元素顺序，哈希表保证唯一
- TreeSet：红黑树，有序，可用于排序

#### BitSet

### Queue

- 元素先进先出
- LinkedList：链表底层，查询慢，增删快，不同步，效率高
- Pop/push/peek
- BlockingQueue
- Deque
- add()和offer()方法都是用来向队列添加元素，如果容量已满，add()会抛出异常，offer()会返回false。
- remove()和pull()方法都是用来从头部删除元素，如果队列为空，remove()会抛出异常，pull会返回null。

### Stack

### Vector

### 集合运算

交集：retainAll

差集：removeAll

并集：addAll + removeAll



🎈：Collection中定义的接口，List，Set接口中又重新定义，子接口方法的描述更加具体，有什么作用？

## Map



[面试官：请说下对理解HashMap及LinkedHashMap的理解（八股文） (qq.com)](https://mp.weixin.qq.com/s/uOu9CvFttKYMHO3yLsSgjQ)



### 4.1 HashMap

- HashMap：key和value都允许null，不同步，父类是AbstractMap
- LinkedHashMap
- WeakHashMap
- HashTable：不允许null，同步，父类是Dictionary

### 4.2 TreeMap

- TreeMap
- IdentityHashMap

```java
package com.jettech;

import java.util.*;
import java.util.function.BiConsumer;

public class Main {

    public static void main(String[] args) throws Exception {
	// write your code here
        TreeSet<Integer> ts = new TreeSet<>();
        ts.add(3);
        ts.add(4);
        ts.add(2);
        Iterator<Integer> ii = ts.iterator();
        while (ii.hasNext()) {
            System.out.println(ii.next());
        }

        HashMap<String, Object> hm = new HashMap<>();
        hm.put("aa", "aa");
        hm.put("bb", "bb");

        Iterator<Map.Entry<String, Object>> it = hm.entrySet().iterator();
        for (Map.Entry<String, Object> so : hm.entrySet()) {
            so.getKey();
            so.getValue();
        }
        

    }
}
```

### WeakHashMap

### SortedMap



## Iterator



## 其他

### 5.1 Collections

- 是一个包装类，包含有各种有关集合的静态操作，不能实例化。
- sort()：自然排序
- shuffle()：随机置换
- reverse()：反转
- fill()：用某个值替换集合中所有元素，可用于初始化集合
- copy()：复制，覆盖对应索引元素
- min/max：比较，可以使用自定义比较器
- rotate：元素向后移动，旋转
- swap：交换元素位置
- binarySearch：查找元素索引
- 其他

### 5.2 Arrays

- 将数组转换为List，使用Arrays.asList()
- 将List转换为数组，使用List的toArray()
- 


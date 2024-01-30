# 集合



## ArrayList

### **要求**

* 掌握 ArrayList 扩容规则

### **扩容规则**

1. ArrayList() 会使用长度为零的数组

2. ArrayList(int initialCapacity) 会使用指定容量的数组

3. public ArrayList(Collection<? extends E> c) 会使用 c 的大小作为数组容量

4. add(Object o) **首次扩容为 10，再次扩容为上次容量的 1.5 倍**

5. addAll(Collection c) 没有元素时，扩容为 Math.max(10, 实际元素个数)，有元素时为 Math.max(原容量 1.5 倍, 实际元素个数)

其中第 4 点必须知道，其它几点视个人情况而定

~~~java
List<String> list = new ArrayList<>();// 此时数组的长度为0
list.add("1");// 数组首次扩容为 10，再次扩容为上次容量的 1.5 倍
~~~





## Iterator

**要求**

* 掌握什么是 Fail-Fast、什么是 Fail-Safe

Fail-Fast 与 Fail-Safe

* ArrayList 是 fail-fast 的典型代表，遍历的同时不能修改，尽快失败

* CopyOnWriteArrayList 是 fail-safe 的典型代表，遍历的同时可以修改，原理是读写分离

**提示**

* 测试代码见 `day01.list.FailFastVsFailSafe`，这里不再列出



## 9. LinkedList

**要求**

* 能够说清楚 LinkedList 对比 ArrayList 的区别，并重视纠正部分错误的认知

**LinkedList**

1. 基于双向链表，无需连续内存
2. 随机访问慢（要沿着链表遍历）
3. 头尾插入删除性能高
4. 占用内存多

**ArrayList**

1. 基于数组，需要连续内存
2. 随机访问快（指根据下标访问）
3. 尾部插入、删除性能可以，其它部分插入、删除都会移动数据，因此性能会低
4. 可以利用 cpu 缓存，局部性原理

> ***代码说明***
>
> * day01.list.ArrayListVsLinkedList#randomAccess 对比随机访问性能
> * day01.list.ArrayListVsLinkedList#addMiddle 对比向中间插入性能
> * day01.list.ArrayListVsLinkedList#addFirst 对比头部插入性能
> * day01.list.ArrayListVsLinkedList#addLast 对比尾部插入性能
> * day01.list.ArrayListVsLinkedList#linkedListSize 打印一个 LinkedList 占用内存
> * day01.list.ArrayListVsLinkedList#arrayListSize 打印一个 ArrayList 占用内存



## 10. HashMap

**要求**

* 掌握 HashMap 的基本数据结构
* 掌握树化
* 理解索引计算方法、二次 hash 的意义、容量对索引计算的影响
* 掌握 put 流程、扩容、扩容因子
* 理解并发使用 HashMap 可能导致的问题
* 理解 key 的设计

### 1）基本数据结构

* 1.7 数组 + 链表
* 1.8 数组 + （链表 | 红黑树）

> 更形象的演示，见资料中的 hash-demo.jar，运行需要 jdk14 以上环境，进入 jar 包目录，执行下面命令
>
> ```
> java -jar --add-exports java.base/jdk.internal.misc=ALL-UNNAMED hash-demo.jar
> ```

### 2）树化与退化

**树化意义**

* 红黑树用来避免 DoS 攻击，防止链表超长时性能下降，树化应当是偶然情况，是保底策略
* hash 表的查找，更新的时间复杂度是 $O(1)$，而红黑树的查找，更新的时间复杂度是 $O(log_2⁡n )$，TreeNode 占用空间也比普通 Node 的大，如非必要，尽量还是使用链表
* hash 值如果足够随机，则在 hash 表内按泊松分布，在负载因子 0.75 的情况下，长度超过 8 的链表出现概率是 0.00000006，树化阈值选择 8 就是为了让树化几率足够小

**树化规则**

* 当链表长度超过树化阈值 8 时，先尝试扩容来减少链表长度，如果数组容量已经 >=64，才会进行树化

**退化规则**

* 情况1：在扩容时如果拆分树时，树元素个数 <= 6 则会退化链表
* 情况2：remove 树节点时，若 root、root.left、root.right、root.left.left 有一个为 null ，也会退化为链表

### 3）索引计算

**索引计算方法**

* 首先，计算对象的 hashCode()
* 再进行调用 HashMap 的 hash() 方法进行二次哈希
  * 二次 hash() 是为了综合高位数据，让哈希分布更为均匀
* 最后 & (capacity – 1) 得到索引

**数组容量为何是 2 的 n 次幂**

1. 计算索引时效率更高：如果是 2 的 n 次幂可以使用位与运算代替取模
2. 扩容时重新计算索引效率更高： hash & oldCap == 0 的元素留在原来位置 ，否则新位置 = 旧位置 + oldCap

**注意**

* 二次 hash 是为了配合 **容量是 2 的 n 次幂** 这一设计前提，如果 hash 表的容量不是 2 的 n 次幂，则不必二次 hash
* **容量是 2 的 n 次幂** 这一设计计算索引效率更好，但 hash 的分散性就不好，需要二次 hash 来作为补偿，没有采用这一设计的典型例子是 Hashtable

### 4）put 与扩容

**put 流程**

1. HashMap 是懒惰创建数组的，首次使用才创建数组
2. 计算索引（桶下标）
3. 如果桶下标还没人占用，创建 Node 占位返回
4. 如果桶下标已经有人占用
   1. 已经是 TreeNode 走红黑树的添加或更新逻辑
   2. 是普通 Node，走链表的添加或更新逻辑，如果链表长度超过树化阈值，走树化逻辑
5. 返回前检查容量是否超过阈值，一旦超过进行扩容

**1.7 与 1.8 的区别**

1. 链表插入节点时，1.7 是头插法，1.8 是尾插法

2. 1.7 是大于等于阈值且没有空位时才扩容，而 1.8 是大于阈值就扩容

3. 1.8 在扩容计算 Node 索引时，会优化

**扩容（加载）因子为何默认是 0.75f**

1. 在空间占用与查询时间之间取得较好的权衡
2. 大于这个值，空间节省了，但链表就会比较长影响性能
3. 小于这个值，冲突减少了，但扩容就会更频繁，空间占用也更多

### 5）并发问题

**扩容死链（1.7 会存在）**

1.7 源码如下：

```java
void transfer(Entry[] newTable, boolean rehash) {
    int newCapacity = newTable.length;
    for (Entry<K,V> e : table) {
        while(null != e) {
            Entry<K,V> next = e.next;
            if (rehash) {
                e.hash = null == e.key ? 0 : hash(e.key);
            }
            int i = indexFor(e.hash, newCapacity);
            e.next = newTable[i];
            newTable[i] = e;
            e = next;
        }
    }
}
```

* e 和 next 都是局部变量，用来指向当前节点和下一个节点
* 线程1（绿色）的临时变量 e 和 next 刚引用了这俩节点，还未来得及移动节点，发生了线程切换，由线程2（蓝色）完成扩容和迁移

![image-20210831084325075](D:\alwaysUse\notes\myNotes\java interview note\山哥面试专题\基础\讲义\img\image-20210831084325075.png)

* 线程2 扩容完成，由于头插法，链表顺序颠倒。但线程1 的临时变量 e 和 next 还引用了这俩节点，还要再来一遍迁移

![image-20210831084723383](D:\alwaysUse\notes\myNotes\java interview note\山哥面试专题\基础\讲义\img\image-20210831084723383.png)

* 第一次循环
  * 循环接着线程切换前运行，注意此时 e 指向的是节点 a，next 指向的是节点 b
  * e 头插 a 节点，注意图中画了两份 a 节点，但事实上只有一个（为了不让箭头特别乱画了两份）
  * 当循环结束是 e 会指向 next 也就是 b 节点

![image-20210831084855348](D:\alwaysUse\notes\myNotes\java interview note\山哥面试专题\基础\讲义\img\image-20210831084855348.png)

* 第二次循环
  * next 指向了节点 a
  * e 头插节点 b
  * 当循环结束时，e 指向 next 也就是节点 a

![image-20210831085329449](D:\alwaysUse\notes\myNotes\java interview note\山哥面试专题\基础\讲义\img\image-20210831085329449.png)

* 第三次循环
  * next 指向了 null
  * e 头插节点 a，**a 的 next 指向了 b**（之前 a.next 一直是 null），b 的 next 指向 a，死链已成
  * 当循环结束时，e 指向 next 也就是 null，因此第四次循环时会正常退出

![image-20210831085543224](D:\alwaysUse\notes\myNotes\java interview note\山哥面试专题\基础\讲义\img\image-20210831085543224.png)

**数据错乱（1.7，1.8 都会存在）**

* 代码参考 `day01.map.HashMapMissData`，具体调试步骤参考视频

> ***补充代码说明***
>
> * day01.map.HashMapDistribution 演示 map 中链表长度符合泊松分布
> * day01.map.DistributionAffectedByCapacity 演示容量及 hashCode 取值对分布的影响
>   * day01.map.DistributionAffectedByCapacity#hashtableGrowRule 演示了 Hashtable 的扩容规律
>   * day01.sort.Utils#randomArray 如果 hashCode 足够随机，容量是否是 2 的 n 次幂影响不大
>   * day01.sort.Utils#lowSameArray 如果 hashCode 低位一样的多，容量是 2 的 n 次幂会导致分布不均匀
>   * day01.sort.Utils#evenArray 如果 hashCode 偶数的多，容量是 2 的 n 次幂会导致分布不均匀
>   * 由此得出对于容量是 2 的 n 次幂的设计来讲，二次 hash 非常重要
> * day01.map.HashMapVsHashtable 演示了对于同样数量的单词字符串放入 HashMap 和 Hashtable 分布上的区别



### 6）key 的设计

**key 的设计要求**

1. HashMap 的 key 可以为 null，但 Map 的其他实现则不然
2. 作为 key 的对象，必须实现 hashCode 和 equals，并且 key 的内容不能修改（不可变）
3. key 的 hashCode 应该有良好的散列性

如果 key 可变，例如修改了 age 会导致再次查询时查询不到

```java
public class HashMapMutableKey {
    public static void main(String[] args) {
        HashMap<Student, Object> map = new HashMap<>();
        Student stu = new Student("张三", 18);
        map.put(stu, new Object());

        System.out.println(map.get(stu));

        stu.age = 19;
        System.out.println(map.get(stu));
    }

    static class Student {
        String name;
        int age;

        public Student(String name, int age) {
            this.name = name;
            this.age = age;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public int getAge() {
            return age;
        }

        public void setAge(int age) {
            this.age = age;
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            Student student = (Student) o;
            return age == student.age && Objects.equals(name, student.name);
        }

        @Override
        public int hashCode() {
            return Objects.hash(name, age);
        }
    }
}
```



**String 对象的 hashCode() 设计**

* 目标是达到较为均匀的散列效果，每个字符串的 hashCode 足够独特
* 字符串中的每个字符都可以表现为一个数字，称为 $S_i$，其中 i 的范围是 0 ~ n - 1 
* 散列公式为： $S_0∗31^{(n-1)}+ S_1∗31^{(n-2)}+ … S_i ∗ 31^{(n-1-i)}+ …S_{(n-1)}∗31^0$
* 31 代入公式有较好的散列特性，并且 31 * h 可以被优化为 
  * 即 $32 ∗h -h $
  * 即 $2^5  ∗h -h$
  * 即 $h≪5  -h$
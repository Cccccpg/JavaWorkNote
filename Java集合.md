# 1、集合框架和底层数据结构
![image.png](https://cdn.nlark.com/yuque/0/2023/png/28551010/1679626675887-e82a0029-f3c1-4578-a2c2-8afa82f3e5f9.png#averageHue=%23f7f7f7&clientId=u4e6dee48-018a-4&from=paste&height=684&id=u28fc4412&name=image.png&originHeight=684&originWidth=945&originalType=binary&ratio=1&rotation=0&showTitle=false&size=49357&status=done&style=none&taskId=u4d23d1dd-c8de-4de1-a604-b280570f4fb&title=&width=945)

**HashSet：**底层数据结构是hash表，基于HashMap实现。
**HashMap：**
	JDK1.8 之前：HashMap 由数组+链表组成的，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的（“拉链法”解决冲突）。 
	JDK1.8 以后：在解决哈希冲突时有了较大的变化，**当链表长度大于阈值（默认为 8）**（将链表转换成红黑树前会判断，**如果当前数组的长度小于 64，那么会选择先进行数组扩容**，而不是转换为红黑树)时，将链表转化为红黑树，以减少搜索时间。  

# 2、哪些集合类是线程安全的？

- **Vector**：就比 arraylist 多了个同步化机制（线程安全），因为效率较低， 现在已经不太建议使用。在 web 应用中，特别是前台页面，往往效率（页面响 应速度）是优先考虑的。 
- **Statck**：堆栈类，先进后出。  
- **HashTable**：就比 HashMap 多了个线程安全。 
- **ConcurrentHashMap**：线程安全的集合类。
- **enumeration**：枚举，相当于迭代器。  
# 3、集合类线程不安全举例
## 3.1 ArrayList
请编写一个ArrayList不安全的案例并给出解决方案？
```java
public class Main{
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        for (int i = 1; i <= 30; i++) {
            new Thread(() ->{
                list.add(UUID.randomUUID().toString().substring(0, 8));
                System.out.println(list);
            }, String.valueOf(i)).start();
        }
    }
}
```
**故障现象：**
这段代码创建了 30 个线程，每个线程都向一个共享的列表 **list** 中添加一个长度为 8 的随机字符串，并输出当前列表内容。由于多个线程同时对 **list** 进行操作，因此会出现线程安全问题。
报java.util.ConcurrentModificationException异常
**解决方案：**
1、使用线程安全的数据结构，比如说`Vector`或者`CopyOnWriteArrayList`。
2、使用 **Collections.synchronizedList()** 方法将非线程安全的 **ArrayList** 转换成线程安全的。
3、在修改共享数据时，使用同步锁保证线程安全。其中使用同步锁时，要注意锁定的对象应该是所有线程共享的，而不是县城内部的局部变量。
```java
public class Main{
    public static void main(String[] args) {
        List<String> list = Collections.synchronizedList(new ArrayList<>());
        for (int i = 0; i < 30; i++) {
            synchronized (list){
                list.add(UUID.randomUUID().toString().substring(0, 8));
                System.out.println(list);
            }
        }
    }
}
```
**建议**：在读多写少时，推荐使用`CopyOnWriteArrayList`，因为这个类里面是通过lock锁来实现线程同步的。
## 3.2 HashSet
**HashSet底层数据结构是HashMap，那么为什么HashSet在执行**`add`**方法时，只添加1个元素，而不是HashMap中同时添加key和value呢？**
查看源码：

```java
private static final Object PRESENT = new Object();

public HashSet() {
    map = new HashMap<>();
}

public boolean add(E e) {
    return map.put(e, PRESENT)==null;
}
```
可以发现，HashSet底层其实就是用HashMap实现的，Set在使用add添加元素的时候，用的就是HashMap的`put`方法，只不过在添加的时候，将需要添加的元素`e`作为key添加，而Value是一个默认的final对象`PRESENT`。
# 4、ArrayList和LinkedList的区别

- **线程安全：**两者都不同步，线程都不安全。
- **底层数据结构：**`ArrayList`底层使用的是Object数组；`LinkedList`底层使用的是双向链表。
- **插入删除元素：**`ArrayList`采用数组存储；`LinkedList`采用链表存储，所以与数据结构中数组和链表特性相同。
- **快速访问：**`ArrayList`可以通过元素序号快速访问对象；`LinkedList`不支持高效的元素访问。
- **内存空间占用：** `ArrayList` 的空间浪费主要体现在在 list 列表的结尾会预留一定的容量空间； `LinkedList` 的空间花费则体现在它的每一个元素都需要消耗比 ArrayList 更多的空间（因为要存放直接后继和直接前驱以及数据）。  
# 5、ArrayList的扩容机制
![image.png](https://cdn.nlark.com/yuque/0/2023/png/28551010/1679883584962-b1a0b6df-a801-4710-b1fe-efe19419117d.png#averageHue=%23f6f6f6&clientId=u44217d40-74b7-4&from=paste&height=684&id=uf8bead80&name=image.png&originHeight=752&originWidth=1422&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=122113&status=done&style=none&taskId=u1abe2f2c-4fa0-4e7e-b057-6a40d0a65d6&title=&width=1292.7272447081643)
`ArrayList`的底层采用`Object`数组来存储数据，在往 `ArrayList` 里面添加元素时，才会涉及到扩容机制。由于采用的是数组存储，所以会给数组设置默认长度10。
当数组中没有元素时，是不会设置为默认长度10 的，数组是一个空数组。只有要添加元素时，会进入 `ensureCapacityInternal()`进行判断，如果数据数组为空数组，在添加第一个元素时才将数组长度扩容为默认值10，如果数组不为空数组 ， 就在 `ensureCapacityInternal()`里 面 调 用 `ensureExplicitCapacity()`来判断是否需要扩容。

如果需要扩容，才调用 `grow()`方法进行扩容。在`grow()`方法里面，会通过右移位运算将数组长度的新长度扩容为**原来的 1.5倍左右（oldCapacity 为偶数就是 1.5 倍，否则是 1.5 倍左右)**。

扩容后如果新容量不能满足所需容量，就将新容量扩大为所需容量。也即新容量大于 `MAX_ARRAY_SIZE`,就调用`hugeCapacity()`方法来比较 `minCapacity` 和 `MAX_ARRAY_SIZE` 的大小，如果 `minCapacity` 大于最大容量，则新容量则为 `Integer.MAX_VALUE`，否则，新容量大小则为 `MAX_ARRAY_SIZE`，即为 `Integer.MAX_VALUE - 8`。

最后将原数组中的元素拷贝到扩容后的新数组， 并将原数组的引用设置为拷贝后的数组。 

# 6、HashSet如何检查重复？
把对象添加到HashSet中时，

1. `HashSet`会计算对象的`hashCode`值，来判断对象加入的位置，同时也会与其他加入的对象`hashCode`值作比较；
2. 如果没有相同的`hashcode`，`HashSet`会假设对象没有重复出现。
3. 如果有相同的`hashCode`，会调用`equals`来检查`hashCode`相同的对象是否真的相同。
4. 如果用equals比较之后，两者相同，那么`HashSet`就不会让这个元素加入。

简单总结：
首先比较hashCode是否相等，再使用equals比较值是否相等。

# 7、HashMap的底层实现
## 7.1 JDK 1.8之前
![image.png](https://cdn.nlark.com/yuque/0/2023/png/28551010/1679887354948-f981415c-e278-4a9e-b7fc-2739449b0cd5.png#averageHue=%23fbf5ee&clientId=u490ccfef-0f43-4&from=paste&height=245&id=u0b5493c3&name=image.png&originHeight=269&originWidth=432&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=12674&status=done&style=none&taskId=u8674cb8d-3668-4827-a78f-3ac24f9e6fc&title=&width=392.7272642151385)
底层采用**数组+链表**，HashMap 通过它的`hash()`方法获取 key 对应的`hashCode`值，然后通过`(n-1) & hash` 判断当前元素存放的位置，如果当前位置存在元素的话，就判断该元素与要存入的 hash 值以及 key 是否相同，如果相同就直接覆盖，不相同的话，就通过拉链法解决 hash 冲突，将元素存放大链表中。

## 7.2 JDK 1.8之后  
![image.png](https://cdn.nlark.com/yuque/0/2023/png/28551010/1679887361430-2c8688f6-ec3a-43dd-b33e-cf971e94b21b.png#averageHue=%23f0eeed&clientId=u490ccfef-0f43-4&from=paste&height=365&id=u9a462031&name=image.png&originHeight=401&originWidth=533&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=18055&status=done&style=none&taskId=u56e2021c-37b0-4666-993c-0323e5ac772&title=&width=484.5454440432149)
JDK1.8 之后底层采用的是**数组+（链表和红黑树）**，主要不同在于解决hash冲突时，当链表长度超过设定的阈值长度时（默认为 8）,会先判断数组的长度是否小于 64(初始最大容量)，如果小于的话，并不是直接转为红黑树，而是进行扩容，只有当数组长度大于 64 时，才会将对应的链表转换为红黑树。
## 7.3 put的具体过程
![image.png](https://cdn.nlark.com/yuque/0/2023/png/28551010/1679886355833-289e85ef-537a-4f3f-a7a9-f37843d33188.png#averageHue=%23f7f6f4&clientId=u490ccfef-0f43-4&from=paste&height=565&id=u62afd134&name=image.png&originHeight=622&originWidth=1116&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=47834&status=done&style=none&taskId=u21ecf30e-b3fb-4267-baa3-f17664ff7de&title=&width=1014.5454325557745)

1. 判断键值对数组 table[i]是否为空或为null，若是，则执行 resize()进行扩容；否则进行下一步 ； 
2. 根据 key计算hash 值得到插入的数组索引 i，如果 table[i]==null，直接新建节点添加，如果 table[i]不为空，则进行下一步； 
3. 判断 table[i]的首个元素是否和 key 一样，如果相同直接覆盖 value，否则进行下一步，这里的相同指的是 hashCode 以及 equals； 
4. 判断 table[i] 是否为 treeNode，即 table[i] 是否是红黑树，如果是红黑树，则直接在树中插入键值对，否则操作链表； 
5. 遍历 table[i]，判断链表长度是否大于 8，大于 8 的话就准备将链表转换为红黑树，在将链表转换为红黑树之前，会进行判断是否真的转为红黑树。
6. 如果当前数组的长度小于64，那么会先进行数组的扩容，否则才会真的转红黑树，然后在红黑树中执行插入操作，否则进行链表的插入操作；遍历过程中若发现 key 已经存在直接覆盖 value 即可；
7. 插入成功后，判断实际存在的键值对数量 size 是否超过了最大容量 `threshold`(数组长度*0.75)，如果超过，进行扩容。  

## 7.4 为什么负载因子是0.75？

loadFactor 太大导致查找元素效率低，存放的数据拥挤，太小导致数组的利用率低，存放的数据会很分散。loadFactor 的默认值为 **0.75f 是官方给出的一个比较好的临界值，可以保证查询性能和空间利用效率之间取得平衡**。

## 7.5 讲一讲HashMap的扩容机制

1. 初始化或达到扩容阈值（数组长度*0.75）时，调用`resize()`方法进行扩容，第一次添加数据初始化数组长度为16；
2. 创建新的数组，新数组长度是原来数组长度的2倍；
3. 将原数组上的数据移到新数组上，其中没有Hash冲突的key，直接计算其在新数组中的索引位置添加；
4. 如果存在Hash冲突的key，先采用链地址法解决，如果是红黑树，则直接在红黑树中添加；如果是链表，则需要拆分链表，要么该元素留在原来的位置，要么移动到原来位置+增加的数组大小这个位置上。
5. 所有的元素移动完毕后，原数组的引用替换为新数组的引用，完成扩容操作。

# 8、HashMap的长度为什么是2的幂次方？
当我们往 HashMap 里面 put 元素的时候，会通过 hashMap 的 hash 方法，获取 key 对应的 hashCode 值，然后拿这个值去判断该元素要存放在那个地方，这里采用的是`(n-1) & hash`,也即右移16位，其中 n 为 HashMap 的长度，这个操作只有当 n 是 2 的幂次方时，`(n-1) & hash` 才和 hash%n 表示的是一个意思，这样才能找到这个 key 在数组中对应的位置。
如果不是 2 的幂次方， `(n-1) & hash` 是不等于 `hash%n` 的，之所以采用位运算的好处是：相较于%操作，它的**效率更高，保证散列均匀分布**。  

# 9、HashMap有哪几种常见的遍历方式？

1. 迭代器（Iterator）方式遍历
```java
// entrySet遍历
Iterator<Map.Entry<Integer, String>> iterator = map.entrySet().iterator();
while (iterator.hasNext()) {
    Map.Entry<Integer, String> entry = iterator.next();
    System.out.println(entry.getKey());
    System.out.println(entry.getValue());
}

// keySet遍历
Iterator<Integer> iterator = map.keySet().iterator();
while (iterator.hasNext()) {
    Integer key = iterator.next();
    System.out.println(key);
    System.out.println(map.get(key));
}
```

2. For Each方式遍历
```java
// entrySet遍历
for (Map.Entry<Integer, String> entry : map.entrySet()) {
    System.out.println(entry.getKey());
    System.out.println(entry.getValue());
}

// keySet遍历
for (Integer key : map.keySet()) {
    System.out.println(key);
    System.out.println(map.get(key));
}
```

3. Lambda表达式遍历
```java
// lambda表达式遍历
map.forEach((key, value) -> {
    System.out.println(key);
    System.out.println(value);
});
```

4. Streams API遍历
```java
// 单线程遍历
map.entrySet().stream().forEach((entry) -> {
    System.out.println(entry.getKey());
    System.out.println(entry.getValue());
});
// 多线程遍历
map.entrySet().parallelStream().forEach((entry) -> {
    System.out.println(entry.getKey());
    System.out.println(entry.getValue());
});
```
## 9.1 性能测试
`entrySet`的性能比`keySet`的性能好，尽量使用`entrySet`来遍历Map集合。
`KeySet` 在循环时使用了 `map.get(key)`，而 map.get(key) 相当于又遍历了一遍 Map 集合去查询 key 所对应的值。为什么要用“又”这个词？那是因为**在使用迭代器或者 for 循环时，其实已经遍历了一遍 Map 集合了，因此再使用 map.get(key) 查询时，相当于遍历了两遍**。
而 `EntrySet `只遍历了一遍 Map 集合，之后通过代码“`Entry<Integer, String> entry = iterator.next()`”把对象的 key 和 value 值都放入到了 Entry 对象中，因此再获取 key 和 value 值时就无需再遍历 Map 集合，只需要从 Entry 对象中取值就可以了。
所以，**EntrySet 的性能比 KeySet 的性能高出了一倍，因为 KeySet 相当于循环了两遍 Map 集合，而 EntrySet 只循环了一遍**。
## 9.2 安全性能
我们不能在遍历中使用集合 map.remove() 来删除数据，这是非安全的操作方式，但我们可以使用迭代器的 iterator.remove() 的方法来删除数据，这是安全的删除集合的方式。同样的我们也可以使用 Lambda 中的 removeIf 来提前删除数据，或者是使用 Stream 中的 filter 过滤掉要删除的数据进行循环，这样都是安全的，当然我们也可以在 for 循环前删除数据在遍历也是线程安全的。
所以，**尽量使用迭代器（Iterator）来遍历EntrySet的遍历方式操作Map集合**
# 10、有哪些解决哈希冲突的方法？

1. 使用链地址法（散列表）来链接拥有相同hash值的数据；
2. 使用2次扰动函数（hash函数）来降低哈希冲突的概率，使得数据分布更平均；
3. 引入红黑树进一步降低遍历的时间复杂度，使得遍历更快。
# 11、为什么HashMap中String、Integer这样的包装类适合作为Key？
String、Integer等包装类的特性能够保证Hash值的不可更改性和计算准确性，能够有效地减少Hash碰撞的几率。
这些包装类都是final类型，即不可变性，可以保证key的不可更改性，不会存在获取hash值不同的情况。
内部已重写`equals`、`hashCode`等方法，遵守了HashMap内部的规范，不容易出现hash值计算错误的情况。
# 12、ConcurrentHashMap和HashTable的区别？
**底层数据结构：**ConcurrentHashMap在JDK1.8之前，采用的是数组+链表来实现；而在JDK1.8及之后的版本，采用的是数组+链表/红黑树。HashTable采用的是数组+链表实现的。
**实现线程安全的方式：**

- 在 JDK1.7 的时候，`ConcurrentHashMap`（分段锁 ReentrantLock） 对整个桶数组进行了分割分段(Segment)，每一把锁只锁容器其中一部分数据，多线程访问容器里不同数据段的数据，就不会存在锁竞争，提高并发访问率。 

![image.png](https://cdn.nlark.com/yuque/0/2023/png/28551010/1679900105138-6350ed5d-09b0-469e-8a0a-c66b9186696f.png#averageHue=%23fbf9f4&clientId=u490ccfef-0f43-4&from=paste&height=352&id=ucb28071b&name=image.png&originHeight=387&originWidth=677&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=22701&status=done&style=none&taskId=u500f5149-232e-449e-950e-3460cf5b416&title=&width=615.4545321149277)

- 到了 JDK1.8 的时候已经摒弃了 Segment 的概念，而是直接用 Node 数组+链表+红黑树的数据结构来实现，并发控制使用 synchronized 和 CAS 来操作。（JDK1.6 以后 对 synchronized 锁做了很多优化） 整个看起来就像是优化过且线程安全的 HashMap，虽然在 JDK1.8 中还能看到 Segment 的 数据结构，但是已经简化了属性，只是为了兼容旧版本；

![image.png](https://cdn.nlark.com/yuque/0/2023/png/28551010/1679900123366-ea60f4b4-7a1c-465a-9068-584e6d8e0a4e.png#averageHue=%23f2f1f0&clientId=u490ccfef-0f43-4&from=paste&height=354&id=u486fb305&name=image.png&originHeight=389&originWidth=689&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=21758&status=done&style=none&taskId=ua0a7f868-0fbe-400a-b4ce-252ce060562&title=&width=626.3636227875704)

- Hashtable(同一把锁) : 使用 synchronized 来保证线程安全，效率非常低下。当一个线程访问同步方法时，其他线程也访问同步方法，可能会进入阻塞或轮询状态，如使用 put 添加元素，另一个线程不能使用 put 添加元素，也不能使用 get，竞争会越来越激烈效率越低。 

![image.png](https://cdn.nlark.com/yuque/0/2023/png/28551010/1679900155258-5a8acf53-d21d-4ef4-b4c8-1009acb1a387.png#averageHue=%23fcf7f1&clientId=u490ccfef-0f43-4&from=paste&height=274&id=u39cddae8&name=image.png&originHeight=301&originWidth=468&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=14410&status=done&style=none&taskId=ucd983145-7113-4aed-8170-4cbc5114c69&title=&width=425.45453623306673) 
# 13、ConcurrentHashMap的线程安全是怎么实现的？
## 13.1 JDK1.8之前
首先将数据分为一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据时，其他段的数据也能被其他线程访问。 
ConcurrentHashMap 是由 Segment 数组结构和 HashEntry 数组结构组成。 Segment 实现了 ReentrantLock,所以 Segment 是一种可重入锁，扮演锁的角色。HashEntry 用于存储键值对数据。  
```java
static class Segment<K,V> extends ReentrantLock implements Serializable {
}
```
一个 ConcurrentHashMap 里包含一个 Segment 数组，Segment 的个数一旦**初始化就不能改变**。 Segment 数组的大小默认是 16，也就是说默认可以同时支持 16 个线程并发写。
Segment 的结构和 HashMap 类似，是一种数组和链表结构，一个 Segment 包含一个 HashEntry 数组，每个 HashEntry 是一个链表结构的元素，每个 Segment 守护着一个 HashEntry 数组里的元素，当对 HashEntry 数组的数据进行修改时，必须首先获得对应的 Segment 的锁。
也就是说，对同一 Segment 的并发写入会被阻塞，不同 Segment 的写入是可以并发执行的。
## 13.2 JDK1.8之后
ConcurrentHashMap 取消了 Segment 分段锁，采用 Node + CAS + synchronized 来保证并发安全。数据结构跟 HashMap 1.8 的结构类似，数组+链表/红黑二叉树。Java 8 在链表长度超过一定阈值（8）时将链表（寻址时间复杂度为 O(N)）转换为红黑树（寻址时间复杂度为 O(log(N))）。
Java 8 中，锁粒度更细，synchronized 只锁定当前链表或红黑二叉树的首节点，这样只要 hash 不冲突，就不会产生并发，就不会影响其他 Node 的读写，效率大幅提升。
## 13.3 总结

- **线程安全实现方式** ：JDK 1.7 采用 Segment 分段锁来保证安全， Segment 是继承自 ReentrantLock。JDK1.8 放弃了 Segment 分段锁的设计，采用 Node + CAS + synchronized 保证线程安全，锁粒度更细，synchronized 只锁定当前链表或红黑二叉树的首节点。
- **Hash 碰撞解决方法** : JDK 1.7 采用拉链法，JDK1.8 采用拉链法结合红黑树（链表长度超过一定阈值时，将链表转换为红黑树）。
- **并发度** ：JDK 1.7 最大并发度是 Segment 的个数，默认是 16。JDK 1.8 最大并发度是 Node 数组的大小，并发度更大。

# 14、了解HashMap在1.7中多线程死循环问题吗？

因为HashMap在1.7中的底层数据结构采用的是数组＋链表实现的，在插入新元素的时候采用的是头插法。而在数组进行扩容的时候，会进行数据迁移，这个过程中有可能会导致死循环。

比如说，现在有两个线程。

线程一：读取到当前HashMap数据中的一个链表，在准备进行扩容的时候，线程二介入。

线程二：读取HashMap，直接进行扩容。因为插入元素采用的是头插法，所以链表的顺序会倒过来，比如原来是AB，扩容后会变成BA，线程二执行结束。

当线程一再回来继续执行的时候就会导致链表出现环结构，从而出现死循环问题。

在1.8中，插入链表的方法更改为尾插法，保持了与扩容前一样的顺序，就避免了出现死循环问题。




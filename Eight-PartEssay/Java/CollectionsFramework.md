# 概念
## 数组与集合区别，常用那些？
数组和集合的区别：

+ 数组是固定长度的数据结构，一旦创建长度就无法改变，而集合是动态长度的数据结构，可以根据需要动态增加或减少元素。
+ 数组可以包含基本数据类型，而集合只能包含对象
+ 数组可以直接访问元素，而集合需要通过迭代器或其他方法访问元素。

常用的一些Java集合类：

1. ArrayList：动态数组，实现了List接口，支持动态增长
2. LinkedList：双向链表，也实现了List接口，支持快速的插入和删除操作。
3. HashMap：基于哈希表的Map实现，存储键值对，通过键快速查找值。
4. HashSet：基于HashMap实现的Set集合，用于存储唯一元素。
5. TreeMap：基于红黑树实现的有序Map集合，可以按照键的顺序进行排序
6. LinkedHashMap：基于哈希表和双向链表实现的Map集合，保持出入顺序或访问顺序
7. PriorityQueue：优先队列，可以按照比较器或元素的自然顺序进行排序



## Java中的集合
在Java中，集合是动态长度的数据结构，但只能存储对象，不能直接访问元素，需要通过迭代器或其他方法访问元素。

集合主要是分为两大类：Collection接口、Map接口

1. Collection接口-存储单值元素的集合
+ 基于List接口实现的集合主要有ArrayList、LinkedList、Vector、Stack，其是有序且可重复的。
+ 基于Set接口实现的集合，其主要是用于存储唯一元素，主要有：HashSet、TreeSet、LinkedHashSet
+ Queue接口，其作为队列，特点是先进先出，主要有：LinkedList(其也可作为队列使用)、PriorityQueue、ArrayDeque(双端队列)
2. Map接口-存储键值对的集合
+ 基于Map接口实现的集合，主要是存储键值对的集合，其主要有：HashMap、LinkedHAshMap、TreeMap、HashTable、ConcurrentHashMap。



## Java中线程安全的集合是什么
Java中线程安全的集合类，分为java.util和java.util.concurrent包。

1. 在java.util包中线程安全的集合主要有两个：
    1. Vector：是线程安全的动态数组，内部方法基本都使用synchronized修饰，使用对象数组存储数据，支持动态扩容，缺点主要为同步开销大，性能较低。
    2. Hashtable：是线程安全的哈希表，使用方法级synchronized锁住整个对象，不支持null键和值，缺点为同步性能开销大，已被ConcurrentHashMap替代。
    3. 补充：Stack继承自Vector，线程安全的栈，Properties继承自Hashtable，线程安全的配置存储。
2. java.util.concurrent包中基本都是线程安全的集合。
    1. 并发Map：
        1. ConcurrentHashMap：在Java1.7之前采用Segment（分段锁）；在Java1.8之后，取消了分段锁，采用了更细的锁策略，数组元素为null：使用CAS操作，数组元素不为null：使用synchronized锁住链表头或树根节点，链表长度超过阈值时转换为红黑树
        2. ConcurrentSkipListMap：实现了一个基于SkipList算法的可排序的并发集合，SkipList是一种可以在对数预期时间内完成搜索、插入、删除等操作的数据结构，通过维护多个指向其他元素的"跳跃"链接来实现高效查找
    2. 并发Set：
        1. ConcurrentSkipListSet：是线程安全的有序的集合。底层是使用ConcurrentSkipListMap实现
        2. CopyOnWriteArraaySet：是线程安全的Set实现，它是线程安全的无序集合，可以将它理解为线程安全的HashSet。CopyOnWriteArraySet和HashSet虽然都继承于共同的父类AbstractSet；但是，HashSet是通过"散列表"实现的，而CopyOnWriteArraySet则是通过"动态数组(CopyOnWriteArrayList)"实现的，并不是散列表。
    3. 并发List：
        1. CopyOnWriteArrayList：它是ArrayList的线程安全的变体，其中所有写操作（add，set等）都是通过对底层数组进行全新复制来实现，允许存储null元素。即当对象进行写操作时，使用了Lock锁做同步处理，内部拷贝了原数组，并在新数组上进行添加操作，最后将新数组替换掉旧数组；若进行的读操作，则直接返回结果，操作过程中不需要进行同步。
    4. 并发Queue：
        1. ConcurrentLinkedQueue：是一个适用于高并发场景下的队列，它通过无锁的方式(CAS)，实现了高并发状态下的高性能。通常，ConcurrentLinkedQueue的性能要好于BlockingQueue。
        2. BlockingQueue：与ConcurrentLinkedQueue的使用场景不同，BlockingQueue的主要功能并不是在于提升高并发时的队列性能，而在于简化多线程间的数据共享。BlockingQueue提供一种读写等待机制，即如果消费者速度较快，则BlockingQueue则可能会被清空，此时消费线程再试图从BlockingQueue读取数据则会被阻塞。反之，如果生产线程较快，则Blocking可能会被装满，此时，线程再试图向BlockingQueue队列装入数据时，便会被阻塞等待。
    5. 并发Deque：
        1. LinkedBlockingDeque：是一个线程安全的双端队列实现。它的内部使用链表结构，每一个节点都维护了一个前驱节点和一个后驱节点。LinkedBlockingDeque没有进行读写锁的分离，因此同一时间内只能有一个线程对其进行操作
        2. ConcurrentLinkedDeque：ConcurrentLinkedDeque是一种基于链接节点的无限并发链表。可以安全地并发执行插入、删除和访问操作。适用于许多线程同时访问一个公告集合的情况。



## Collections和Collection的区别
+ Collection：
    - Collection是一个接口，是所有集合类的基础接口（注意：Map并不在其中）。
    - Collection接口定义了增删改查等通用的操作和方法。
    - 具体实现类有List、Set、Queue等。
+ Collections：
    - Collections是Java提供的一个工具类，位于java.util包中。
    - 它提供了一系列的静态方法，用于对集合（如List和Set）进行操作和算法，包括排序、查找、替换、反转、随机化等。



## 集合遍历的方法有那些
在Java中，集合的遍历方法主要有以下几种：

+ 普通for循环
+ 增强for循环（for-each循环）
+ Iterator迭代器
+ ListIterator列表迭代器
+ 使用forEach方法
+ Stream API



# List
## 讲一下java里面List的几种实现，几种实现有什么不同
+ ArrayList：动态数组，因本身不具备线程安全，故性能较好；每次扩容时，增加50%。
+ Vector：是具备线程安全的动态数组，性能开销较大。每次扩容时，增加一倍。
+ LinkedList：是Java提供的双向链表，不具备线程安全
+ 在什么场景下运用那种
    - Vector和ArrayList作为动态数组，适用于需要随机访问的场合，但在插入和删除元素上，性能会相对较差。
    - LinkedList进行节点的插入和删除要高效的多，但是随机访问性能要比动态数组慢。

## List可以一边遍历，一边修改元素吗？
在Java中，List在遍历过程中是否可以 修改元素取决于遍历方式和具体的List实现类。

+ 在普通for循环遍历：可以在遍历过程中修改元素，只要修改的元素不超过List范围即可
+ 使用forEach循环遍历：一般不建议在forEach循环中直接修改正在遍历的List元素，因为可能会抛出ConCurrentModificationException异常。因为forEach循环底层是基于迭代器实现的，在遍历过程中修改集合结构，会破坏迭代器的内部状态（这会导致迭代器的预期结构和实际结构不一致）。
+ 使用迭代器遍历时：可以使用迭代器的remove方法来删除元素，但要替换元素的值，对于不可变对象（如：Integer、String），必须通过ListIterator的set方法来进行，而不是直接通过List的Set方法，否则会抛出ConCurrentModificationException异常。
+ 对于线程安全的List，如CopyOnWriteArrayList，由于其采用了写时复制的机制，在遍历的同时可以进行修改操作，不会抛出ConCurrentModificatioinException异常，但可能会读取到旧的数据，因为修改操作是在新的副本上进行的。



## List如何快速删除某个指定下标的元素
+ ArrayList：
    - 通过使用remove(int index)方法来删除指定下标的元素。在删除后会将后续元素向前移动。它的时间复杂度取决于删除的是否是列表末尾的元素，如果是，则为O(1)，如果不是，这是O(n)，n为列表中的元素个数（因为需要向后移动）。
+ LinkedList：
    - 也是使用remove(int index)方法来删除指定下标的元素。但是他需要先遍历到指定的下标位置，然后通过修改链表的指针来删除元素，时间复杂度为O(n),如果修改的是头/尾节点，可以直接通过修改头指针或尾指针来实现删除，时间复杂度为O(n)。
+ CopyOnWriteArrayList：
    - 它的remove方法也可以删除指定下标的元素。其在进行写操作时会创建一个新的数组，故删除操作的时间复杂度取决于数组的复制速度，通常为O(n)，n为数组长度。
    - 但在并发环境下，它的删除操作不会影响读操作，具有较好的并发性能。



## ArrayList和LinkedList的区别，那个是线程安全的
+ ArrayList是通过数组实现的，可以通过索引进行快速访问，它的随机访问效率较高，时间复杂度为O(1)，扩容时，是通过创建一个更大的新数据，再将旧数组的数据复制到新数组中，并删除旧数组。
+ LinkedList是通过链表实现的，通过节点之间的指针进行元素的访问和操作，它在删除和插入操作的性能较好，但是随机访问效率较低，时间复杂度为O(n)，需要遍历数组。在扩容时，通过插入节点来进行扩容。
+ ArrayList在创建时需要分配一段连续的内存空间，因此会占用较大的空间。LinkedList每个节点只需要存储元素和指针，因此相对较小。
+ 二者的插入和删除效率不同，ArrayList在尾部的插入和删除操作效率较高，在中间或开头的插入和删除效率较低，需要移动元素。ListedList在任意位置的插入和删除的效率都比较高，因为只需要调整节点之间的指针，但是LinkedList是不支持随机访问的，所以除了头节点外的插入和删除的时间复杂度都是O(n)，效率也不是很高，故也没有多少人用。
+ 二者都不具备线程安全，Vector是线程安全的



## ArrayList和Vector区别是什么
+ 线程安全方面：ArrayList不具备线程安全，Vector线程安全
+ 扩容方面：ArrayList扩容为原来的1.5倍，Vector扩容为原来的2倍。同时，二者之间，只用Vector可以通过构造方法指定增长因子，灵活控制扩容幅度。
+ 性能方面：ArrayList因不需要额外处理同步问题，故它的性能比Vector要好
+ 补充：使用ArrayList时，需要线程安全时，可以通过Collections.synchronizedList()方法将ArrayList包装成线程安全的集合，灵活性更高



## ArrayList线程安全吗？把ArrayList变成线程安全有那些方法？
+ ArrayList不是线程安全的
+ 可以使用Collections.synchronizedList()方法将ArrayList包装成线程安全的集合
+ 使用CopyOnWriteArrayList来替换ArrayList
+ 使用Vector来替换ArrayList



## 为什么ArrayList不是线程安全的，具体来说那里不安全
在高并发添加数据下，ArrayList会暴露三个问题：

+ 部分值为null（我们并没有add null进去）
    - 当线程1走到了扩容那里发现当前size是9，而数组容量是10，所以不用扩容，这时候cpu让出执行权，线程2也进来了，发现size是9，而数组容量是10，所以不用扩容，这时候线程1继续执行，将数组下标索引为9的位置set值了，还没有来得及执行size++，这时候线程2也来执行了，又把数组下标索引为9的位置set了一遍，这时候两个先后进行size++，导致下标索引10的地方就为null了。
+ 索引越界
    - 线程1走到扩容那里发现当前size是9，数组容量是10不用扩容，cpu让出执行权，线程2也发现不用扩容，这时候数组的容量就是10，而线程1 set完之后size++，这时候线程2再进来size就是10，数组的大小只有10，而你要设置下标索引为10的就会越界（数组的下标索引从0开始）；
+ size与我们add的数量不符
    - 线程1走到扩容那里发现当前size是9，数组容量是10不用扩容，cpu让出执行权，线程2也发现不用扩容，这时候数组的容量就是10，而线程1 set完之后size++，这时候线程2再进来size就是10，数组的大小只有10，而你要设置下标索引为10的就会越界（数组的下标索引从0开始）；

根本原因总结：

+ 检查-执行非原子性：ensureCapacityInternal()和elementData[size++]不是原子操作
+ 共享变量无保护：size和elementData被多个线程同时读写
+ 缺少内存屏障：线程间的修改对其他线程不可见
+ 复合操作竞争：多个操作步骤之间可能被其他线程介入



## ArrayList和LinkedList的应用场景？
+ ArrayList：
    - 频繁的随机访问
    - 数组大小不频繁改变
+ LinkedList：
    - 频繁在中间进行插入删除操作和不需要进行随机访问的场景
    - 集合大小经常改变时，也可以考虑使用LinkedList



## ArrayList的扩容机制
+ 计算新的容量：新数组大小为原数组的1.5倍
+ 创建新的数组
+ 将元素复制：将旧数组中的数据复制到新数组中
+ 更新引用：即将ArrayList内部指向原数组的引用指向新数组
+ 完成扩容：扩容完成后，继续添加新的元素



## 线程安全的List，CopyOnWriteArrayList时如何实现线程安全的。
CopyOnWriteArrayList底层也是通过一个数组保存数据，使用volatile关键字修饰数组，保证当前线程对数组对象重新赋值后，其他线程可以及时感知到。

```plain
private transient volatile Object[] array;
```

并在写入操作时，加了一把互斥锁ReentrantLock以保证线程安全。

```plain
public boolean add(E e){
//获取锁
  final ReeantrantLock lock = this.lock;
  //加锁
  lock.lock();
  try{
  //获取当前List集合保存数据的数组
    Object[] elements=getAarray();
    //获取该数组的长度
    int len = elements.length;
    //将当前数组拷贝一份的同时，让其长度加一
    Object[] newElements = Arrays.copyOf(elements,len+1);
    //将加入的元素放在新数组最后一位
    newElements[len] = e;
    //替换引用，将数组的引用指向新数组的地址
    setArray(newElements);
    return ture;
   } finally{
   //释放锁
     lock.unlock();
   }
}
```

在执行替换地址操作之前，读取的是老数组的数据，数据是有效数据；执行替换地址操作后，读取的是新数组的数据，同样也是有效数据，而且使用该方式能比读写都加上要更加的效率。

读操作是没有加锁的，故读是一直都能读

```plain
public E get(int index){
  return get(getArray(),index);
}
```

## List<>里面填基本数据类型为什么会报错？
List<>等泛型集合类要求填充的必须是引用类型（对象类型），而不能直接使用基本数据类型（如int、char、double等），否则会编译报错。

这是因为Java的泛型机制在设计是就只支持引用类型，不支持基本数据类型。

解决办法是，使用基本数据类型对应的包装类。

这么设计的原因是：

+ 泛型的类型擦除机制：Java泛型在编译后会被擦除为Object类型，而Object只能接收引用类型，不能接受基本数据类型。
+ 历史原因：Java最初设计时基本数据类型和引用数据类型时严格区分的，泛型时后期(JDK1.5)才引入的特性，为了兼容已有的类型系统，选择只支持引用类型。

通过使用包装类，结合Java的自动装箱和自动拆箱机制，可以很方便地在泛型集合中操作基本数据类型的数据。



## List和数组如何互相转换
1. List转数组:主要有两种方式：核心是使用List的toArray()方法，重点注意[泛型和类型匹配]
    1. 无参toArray()(返回Object[]，强转可能报错(ClassCastException)，仅适合不确定数组类型的场景，不推荐)
    2. 带参toArray(T []a)（推荐，指定类型，若传入的数组长度不足，会自动创建新数组，推荐传空数组(JDK会优化长度 )）
2. 数组转List：核心是用Arrays.asList()，但需要注意[返回的List不可变]和[基本类型数组的坑]:
    1. 普通对象数组转List(常用)：Arrays.asList()返回的List不是ArrayList，而是Arrays的内部类，不支持添加/删除操作，想修改就套一层ArrayList。
    2. 基本类型数组转List：

```plain
int[] numArr={1,2,3};
//错误示例，int[]转List会变成List<int[]>，而非List<Integer>
List<Integer> wrongList=Arrays.asList(numArr)

//正确方式1：手动装箱（JDK8-）
List<Integer> numList1=new ArrayList<>();
for(int num:numArr){
  numList1.add(num);
}

//正确方式2：Stream流（JDK8+）
List<Integer>numList2=Arrays.Stream(numArr).boxed().collectors.toList());
```

基本类型数组直接使用Arrays.asList()会把整个数组当成一个元素，必须手动装箱或用Stream流转换为包装类(Integer)的List



# Set
## Java集合中List和Set区别是什么？
Java里的List和Set作为Collection的核心子接口，最核心的区别就是“是否允许元素重复”和“是否保证有序”，原理和使用场景也因此完全不同。

+ List是有序的集合，整这里的“有序”指的是元素的存储顺序和添加顺序一致，而且允许元素重复，甚至可以存多个null值。适合需要按顺序存储、频繁根据位置访问元素的场景，比如购物车列表、订单明细这类要保留添加顺序的场景。
+ 再看Set，它的核心是“元素唯一”，不允许重复，最多只能存一个null值，而且默认不保证元素的存储顺序（除了TreeSet、LinkedHashSet这类特殊实现）。适合需要去重的场景，比如用户标签、商品分类、抽奖名单(避免同一个永福重复中将)这类不允许重复元素的场景。
+ List里的Vector是线程安全的，但性能差，现在基本不用；Set里的LinkedHashSet既保证元素唯一，又能保留添加顺序，TreeSet则会按元素大小排序，而ArrayList、HashSet都是非线程安全的



## 如何对Se排序
Java里Set本身默认不保证有序，但是实现Set的排序，核心是选带排序特性的Set实现类，或把普通Set转成有序结构。

+ TreeSet底层是红黑树，插入时自动排序，支持‘自然排序’（元素实现Comparable）和‘自定义Comparator排序’
+ 如果只按‘插入排序’遍历，不用按元素值排序，用LinkedHashSet即可，性能比TreeSet高。



# Map
## 常见的Map集合
+ 非线程安全
    - HashMap是基于哈希表实现的Map，它根据键的哈希值来存储和获取键值对，JDK1.8中是用数组+链表+红黑树来是实现的
    - LinkedHashMap继承自HashMap，再HashMap的基础上，使用双向链表维护了键值对的插入顺序或访问顺序，使得迭代顺序与插入顺序或访问顺序一致
    - TreeMap：基于红黑树实现，可对键进行排序，默认按自然顺序排序，也可通过指定的比较器进行排序。
+ 线程安全：
    - HashTable的实现方式与HashMap类似，但在方法上了synchronized关键字来保证线程安全。
    - ConCurrentHashMap：在JDK1.8以前采用分段锁来提高并发性能。在JDK1.8以后是通过volatile+CAS或者synchronized来保证线程安全的



## 如何对map进行快速遍历
+ 使用for-each循环和entrySet()方法：可以同时获取Map中的键和值。
+ 使用for-each循环和keySet()方法：只遍历Map中的键，相对简单，性能好。
+ 使用迭代器：获取Map的entrySet()或keySet的迭代器，实现对Map的遍历，在删除元素等操作时比较有用
+ 使用Lambda表达式和forEach()方法：可以在Java8及以上版本使用，更加简洁和函数式。
+ 使用Stream API：将Map转换为流，然后进行各种操作。



## HashMap实现原理
在Java1.7版本之前，HashMap数据结构时数组和链表，HashMap通过哈希算法将元素的键映射到数组中的槽位（Bucket）。如果多个键映射到同一个槽位，他们会以链表的形式存储在用一个槽位上，因为链表的查询时间是O(n)，所以冲突很严重。

在JDK1.8版本时就做了优化，当链表长度超过8时就转换数据结构，不再使用链表存储，而是使用红黑树，在查找时使用红黑树，时间复杂度为O（log n），可以提高查询性能。



## HashMap链表发生转换后为什么不用平衡二叉树？
AVL树是严格平衡的二叉树，要求任意节点的左右子树高度不超过1，这意味着：

+ 插入/删除时会触发大学旋转操作
+ 而HashMap的场景是"链表转树"仅发生在链表长度大于>=8（JDK1.8）时，本身是低频场景，为了这种低频场景付出高频的平衡开销，完全不划算。
+ 红黑树仅保证黑色高度平衡(不是严格的节点高度平衡)，旋转次数远少于AVL树，出入/删除的平均时间复杂度仍为O(Logn)，但实际执行效率更高。

HashMap的核心是"哈希+数组+链表/树"，树的作用只是解决哈希冲突严重导致链表过长的问题，而非做纯树形存储：

+ 红黑树的查找、插入、删除的时间复杂度都是O(logn)，虽然比AVL树的查找略慢（因为高度可能稍高），但增删的开销远低于AVL树；
+ 对于HashMap来说，"增删"和"查找"的频率几乎持平，红黑树的综合性能更优，毕竟HashMap不会只查不改，也不会只改不查。



## 了解哈希冲突解决方法有哪些？
+ 链表法：使用链表或其他数据结构来存储冲突的键值对，将它们连接在同一个哈希通中。
+ 开放寻址法：在哈希表中找到另一个可用的位置来存储冲突的键值对，而不是存储在链表中。常见的有线性探测、二次探测和双重散列
+ 再哈希法：当发生冲突时，使用另一个哈希函数再次计算键的哈希值，直到找到一个空槽来存储键值对。
+ 哈希桶扩容：当哈希冲突过多时，可以动态地扩大哈希桶的数量，重新分配键值对，以减少冲突的概率。



## HashMap是线程安全的吗？
HashMap不是线程安全的，HashMap在多线程会存在下面的问题：

+ JDK1.7HashMap采用数组+链表的数据结构，多线程背景下，在数组扩容时，存在Entry链死循环和数据丢失问题。
+ JDK1.8HashMap采用数组+链表+红黑树的数据结构，优化了1.7中数组扩容的方案，解决了Entry链死循环和数据丢失问题，但是在多线程背景下，put方法存在数据覆盖的问题。

可以通过一下方法来保证线程安全：

+ 使用Collections.synchronizedMap同步加锁的方式，还可以使用HashMap，但是同步性能不达标，而ConCurrentHashMap更适合高并发场景使用。
+ ConCurrentHashMap在JDK1.7和1.8版本改动较大，1.7使用Segment+HashEntry分段锁的方式实现，1.8则抛弃了Segment，改为使用CAS+synchronized+Node实现，同样加入了红黑树，避免链表过长导致性能的问题。



## 在Java中HashMap中get一个元素的过程是怎样的?
get方法的作用是传入需要获取的节点的key，然后将这个节点的value返回，get方法的代码：

```plain
public V get(Object key){
  Node<K,V> e;
  return (e=getNode(hash(key),key))==null?null:e.value;
}
```

可以看到get方法的代码非常简洁，因为具体的代码都封装在了getNode这个方法里面，get方法只是对它进行了调用。

getNode方法接收两个参数，第一个参数是key的hash值，第二个参数是key本身。



## HashMap的put过程介绍一下
HashMap的put()方法用于向HashMap中添加键值对，当调用HashMap的put()方法时，会按照一下详细流程执行(JDK1.8版本)：

1. 根据要添加的键的哈希码计算在数组中的位置（索引）
2. 检查该位置是否为空(即没有键值对存在)
    1. 如果为空，则直接在该位置创建一个新的Entry对象来存储键值对。将要添加的键值对作为该Entry的键和值，并保存在数组的对应位置。将HashMap的修改次数(modCount)加1，以便在进行迭代是发现并发修改。
3. 如果该位置已经存在其他键值对，检查该位置的第一个键值对的哈希码和键是否与要添加的键值对相同？
    1. 如果相同，则表示找到了相同的键，直接将新的值替换旧的值，完成更新。
4. 如果第一个键值对的哈希码和键不相同，则需要遍历链表或红黑树来查找是否有相同的键：如果键值对集合是链表结构，从链表的头部开始逐个比较键的哈希码和equals()方法，直到找到相同的键或达到链表末尾。
    1. 如果找到了相同的键，则使用新的值取代旧的值，即更新键对应的值
    2. 如果没有找到相同的键，则将新的键值对添加到链表的头部
5. 如果键值对集合是红黑树结构，在红黑树中使用哈希码和equals()方法进行查找。根据键的哈希码，定位到红黑树中的某个节点，然后逐个比较键，直到找到相同的键或达到红黑树末尾。
    1. 如果找到了相同的键，则使用新的值取代旧的值，即更新对应的值。
    2. 如果没有找到相同的键，则将新的键值添加到红黑树中
6. 检查链表长度是否达到阈值(默认为8)：
    1. 如果链表长度超过阈值，且HashMap的数组长度大于等于64，则会将链表转换为红黑树，以提高查询效率。
7. 检查负载因子是否超过阈值(默认0.75):
    1. 如果键值对的数量(size)与数组长度的比值大于阈值，则需要进行扩容操作
8. 扩容操作：
    1. 创建一个新的两倍大小的数组
    2. 将旧数组中的键值对重新计算哈希码并分配到新数组中的位置。
    3. 更新HashMap的数组引用和阈值参数。
9. 完成添加操作

注意，HashMap是非线程安全的，如果在多线程环境下使用，需要采取额外的同步措施或使用线程安全的ConCurrrentHashMap。

## HashMap的put(key,val)和get(key)过程
+ 存储对象时，我们将K/V传给put方法，它调用hashCode计算hash从而得到bucket位置，进一步存储，HashMap会根据当前bucket的占用情况自动调整容量（超过Load Facoter 则resize为原来的2倍）。
+ 获取对象时，我们将k传给get，它调用hashCode计算hash从而得到bucket位置，并进一步调用equals()方法确定键值对。如果发生碰撞的时候，HashMap通过链表将产生碰撞冲突的元素组织起来，在Java8中，如果一个bucket中碰撞冲突的元素超过某个限制(默认是8)，则使用红黑树来替代链表，从而提高速度





## HashMap调用get方法一定安全吗？
不是，调用get方法需要注意一下几点：

+ 空指针异常(NullPointerException)：如果尝试使用null作为键调用get方法，而HashMap没有被初始化（即为null），那么会抛出空指针异常。不过，如果HashMap已经初始化，使用null作为键是允许的，因为HashMap支持null键。
+ 线程安全：HashMap本身不是线程安全的。如果在多线程环境中，没有适当的同步措施，同时对HashMap进行读写操作可能会导致不可预测的行为。例如，在一个线程中调用get方法读取数据，而另外一个线程同时修改了结构(如增加或删除元素)，可能会导致读取操作得到错误的结果或抛出ConCurrentModificationException。如果需要再多线程环境中使用类似HashMap的数据结构，可以考虑使用ConCurrentHashMap。

## HashMap一般用什么做key？为啥String适合做key？
+ String对象的不可变性（一旦创建就不能被修改，确保了key的稳定性）
+ 如果Key是可变的，可能会导致hashCode和equals方法的不一致，进而影响HashMap的正确性。



## 为什么HashMap要用红黑树而不是平衡二叉树？
+ 平衡二叉树追求的是一种“完全平衡”状态：任何几点的左右子树的高度差不会超过1，优势是树的节点是很平均分配的。这个要求实在是太严 了，导致每次进行插入/删除节点的时候，几乎都会破坏平衡树的第二个规则，进而我们都需要通过左旋和右旋来进行调整，使之再次成为一颗符合要求的平衡树
+ 红黑树不追求这种完全平衡状态，而是追求一种“弱平衡”状态：整个树最长路径不会超过最短路径的2倍。优势是虽然牺牲了一部分查找的性能效率，但是能够换取一部分维持树平衡状态的成本。与平衡树不同的是，红黑树在插入、删除等操作，不会像平衡树那样，频繁地破坏红黑树的规则，所以不需要频繁地调整，这也是我们为什么大多数情况下使用红黑树的原因。



## HashMap Key可以为null
可以为null。

+ HashMap中使用hash()方法来计算key的哈希值，当key为空时，直接另key的哈希值为0，不走key.hashCode()方法。
+ HashMap虽然支持key和value为null，但是null作为key只能有一个，null作为value可以有多个。



## 重写HashMap的equals和hashCode方法需要注意什么？
HashMap使用key对象的hashCode()和equals方法去决定key-value对的索引。当我们试着从HashMap中获取值的时候，这些方法也会被用到。如果这些方法没有被正确地实现，在这种情况下，两个不同key也许会产生相同的hashCode()和equals()输出，HashMap会认为它们是相同的，然后覆盖它们，而非把它们存储到不同的地方。

同样的，所有不允许存储重复数据的集合类都使用hashCode()和equals()去查找重复，所以正确实现他们非常重要。equals()和hashCode()的实现应该遵循以下规则：

+ 如果o1.equals(o2)为true，那么o1.hashCode()==o2.hashCode()总是为true。
+ 如果o1.hashCode(）==o2.hashCode()，并不意味着o1.equals(o2)会为true。



## 重写HashMap的equals方法不当会出现什么问题
HashMap在比较元素时，会先通过hashCode进行比较，相同的情况下，在通过equals进行比较。

所以equals相等的两个对象，hashCode一定相等。hashCode相等的两个对象，equals不一定相等(比如散列冲突的情况)

重写equals方法，不重写hashCode方法时，可能会出现equals方法返回true，而hashCode方法却返回false，这样的一个后果会导致在HashMap等类中存储多个一模一样的对象，导致出现覆盖存储的数据的问题，这与HashMap只能有唯一的key的规范不符合。

  

## 列举HashMap在多线程下可能会出现的问题？
+ JDK1.7中的HashMap使用头插法插入元素，在多线程的环境下，扩容的时候有可能导致环形链表的出现，形成死循环。因此，JDK1.8使用尾插法插入元素，在扩容时会保持链表元素原本的顺序，不会出现环形链表的问题。
+ 多线程同时执行put操作，如果计算出来的索引位置是相同的，那么会造成前一个key被后一个key覆盖，从而导致元素的丢失。此问题在JDK1.7和JDK1.8中都存在。



## HashMap的扩容机制
HashMap默认的负载因子是0.75，即如果HashMap中的元素个数超过了总容量75%，则会触发扩容，扩容分为两个步骤：

1. 对哈希表长度的扩展(2倍)
2. 将就哈希表中的数据放到新的哈希表中。

因为使用的是2次幂的扩展，所以，元素的位置要么是在原位置，要么是在原位置再移动2次幂的位置。

我们在扩充HashMap时，不需要重新计算hash，只需要看看原来的hash值新增的那个bit是1还是0就好了，是0的话索引没变，是1的话索引变成"原索引+oldCap"。

这样的设计非常巧妙，省去了重新计算hash值的时间，而且同时，由于新增的1bit是0还是1可以认为是随机的，因此resize的过程，均匀的把之前的冲突的节点分散到新的bucket了。



## HashMap的大小为什么是2的n次方大小
HashMap的容量被设计为2的n次方大小，主要是为了通过位运算优化性能并保证元素均匀分布。具体来说，当容量为2^n时，计算元素数组下标的操作`hash % length`可以等价替换为高效的位运算`hash & (length-1)`，这比取模运算快得多。同时，`length-1`的二进制形式是全1（例如容量16对应1111），能确保哈希值的所有低位都参与运算，充分利用了哈希值信息，减少了哈希冲突。此外，这种设计在扩容时（容量翻倍）也能高效地重新分配元素，每个元素的新位置要么保持不变，要么是原索引加上旧容量，提升了扩容效率。



## 往HashMap存20个元素，会扩容几次？
当插入20个元素是，HashMap的扩容过程如下：

1. 初始容量：16
    1. 插入第1到第12个元素时，不需要扩容。
    2. 插入第13个元素时，达到负载因子限制，裤腰扩容。此时，HashMap的容量从16扩容到32.
2. 扩容后的容量：32
    1. 插入第14到第24个元素，不需要扩容
    2. 因此，总共会进行一次扩容。



## 说说HashMap的负载因子
HashMap负载因子LoadFactor的默认值是0.75，当HashMap中的元素个数超过了容量的75%时，就会进行扩容。

默认负载因子为0.75，是因为它提供了空间和时间复杂度之间的良好平衡。

负载因子太低会导致大量的空桶浪费空间，负载因子太高会导致大量的碰撞，降低性能。0.75的负载因子在这两个因数之间取得了良好的平衡。



## HashMap和HashTable有什么不一样的？HashMap一般怎么用？
+ HashMap线程不安全，效率高一点，可以存储null的key和value，null的key只能有一个，null的value可以有多个。默认初始容量为16，每次扩容变为原来2倍。创建时如果给定了初始容量，则扩充为2的幂次方大小。底层数据结构为数组+链表，插入元素后如果链表长度大于阈值(默认为8)，先判断数组长度是否小于64，如果小于，则扩充数组，反之将链表化为红黑树，以减少搜索时间。
+ HashTable线程安全，效率第一点，其内部方法基本都经过synchronized修饰，不可以有null的key和value。默认初始容量为11，每次扩容变为原来的2n+1.创建时给定了初始容量，会直接用给定的大小。底层数据结构为数组+链表。他基本被淘汰了，要保证线程安全可以用ConCurrentHashMap
+ 怎么用：HashMap主要用来存储键值对，可以调用put方法向其中加入元素，调用get方法获取某个键对应的值，也可以通过containsKey方法查看某个键是否存在等。



## ConCurrentHashMap怎么实现
### JDK1.7ConCurrentHashMap：  
在JDK1.7中它使用的是数组加链表的形式实现的，而数组又分为：大数组Segment和小数组HashMapEntry。Segment是一种可重入锁（ReentrantLock），在ConCurrentHashMap里扮演锁的角色；HashEntry则用于存储键值对数据。一个ConCurrentHashMap里包含一个Segment数组，一个Segment里包含一个HashEntry数组，每个hashEntry是一个链表结构的元素。   JDK1.7ConCurrentHashMap分段锁技术将数据分成一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他的数据也能被其他线程访问，能够实现真正的并发访问。

### JDK1.8ConCurrentHashMap：
 在JDK1.7中，ConCurrentHashMap虽然是线程安全的，但因为它的底层实现是数组+链表的形式，所以在数据比较多的情况下访问是很慢的，因为要遍历整个链表，而JDK1.8则使用了数组+链表/红黑树的方式优化了ConCurrentHashMap的实现。

JDK1.8ConCurrentHashMap主要通过volatile加CAS或者synchronized来实现的线程安全的。添加元素时首先会判断容器是否为空：

+ 如果为空则使用volatile加CAS来初始化
+ 如果容器不为空，则根据存储的元素计算该位置是否为空。
    - 如果根据存储的元素计算结果为空，则利用CAS设置该节点
    - 如果根据存储的元素计算结果不为空，则使用synchronized，然后，遍历桶中的数据，并替换或新增节点到桶中，最后再判断是否需要转为红黑树，这样就能保证并发访问时的线程安全了。

如果把上面的执行用一句话归纳的话，就相当于是ConCurrentHashMap通过对头节点加锁来保证线程安全的，锁的粒度相比Segment来说更小了，发生冲突和加锁的频率降低了，并发操作的性能就提高了。

而且JDK1.8使用的是红黑树优化了之前的固定链表，那么当数据量比较大的时候，查询性能也得到了很大的提升，从之前的O(n)优化到了O(logn)的时间复杂度。



## 分段锁怎么加锁？
在ConCurrentHashMap中，将整个数据结构分为多个Segment，每个Segment都类似于一个小的HashMap，每个Segment都有自己的锁，不同Segment之间的操作互不影响，从而提高并发性能。

在ConCurrentHashMap中，对于插入、更新、删除等操作，需要先定位到具体的Segment，然后再在该Segment上加锁，而不是像传统的HashMap一样对整个数据结构加锁。这样可以使得不同Segment之间的操作并行进行，提高了并发性能。



## 分段锁是可重入的吗？
JDK1.7ConCurrentHashMap中的分段锁是用了ReentrantLock，是一个可重入的锁。



## 已经用了synchronized，为什么还要用CAS呢？
ConCurrentHashMap使用这两种手段来保证线程安全主要是一种权衡的考虑，在某些操作中使用synchronized，还是使用CAS，主要是根据锁竞争程度来判断的。

比如：在putVal中，如果计算出来的hash槽没有存放元素，那么就可以直接使用CAS来进行设置值，这是因为在设置元素的时候，因为hash值经过了各种扰动后，造成hash碰撞的几率较低，那么我们可以预测使用较少的自旋来完成具体的hash落槽操作。

当发生了hash碰撞的时候说明容量不够用了或者已经有大量线程访问了，因此这时候使用synchronized来处理hash碰撞比CAS效率要高，因为发生了hash碰撞大概率来说是线程竞争比较强烈。



## ConCurrentHashMap用来悲观锁还是乐观锁？
  悲观锁和乐观锁都有用到。

添加元素时首先会判断容器是否为空：

+ 如果为空则使用volatile加CAS(乐观锁)来初始化。
+ 如果容器不为空，则根据存储的元素计算该位置是否为空。
+ 如果根据存储的元素计算结果为空则利用CAS设置改节点。
+ 如果根据存储的元素计算结果不为空，则使用synchronized(悲观锁)，然后遍历桶中的数据，并替换或新增节点到桶中，最后再判断是否需要转为红黑树，这样就能保证并发访问时的线程安全了。



## HashTable底层实现原理是什么？
+ HashTable的底层数据结构主要是数组加上链表，数组是主体，链表是解决hash冲突存在的。
+ HashTable是线程安全的，实现方式是HashTable的所有公共方法均采用synchronized关键字，当一个线程访问同步方法，另一个线程也访问的时候，就会陷入阻塞或者轮询的状态。



## HashTable线程安全是怎么实现的？
因为它的put，get做成了同步方法，保证了HashTable的线程安全，每个操作数据的方法都进行了同步控制之后，由此带来的问题任何一个时刻只能有一个线程可以操纵HashTable，所以其效率比较低。

因此，HashTable是通过使用了synchronized关键字来保证其线程安全。



在Java中，可以使用synchronized关键字来标记一个方法或者代码块，当某个线程调用该对象的synchronized方法或者访问synchronized代码块时，这个线程便获得了该对象的锁，其它线程暂时无法访问这个方法，只有等待这个方法执行完毕或者代码块执行完毕，这个线程才会释放该对象的锁，其他线程才能执行这个方法或者代码块。



## HashTable和ConCurrentHashMap有什么区别:
### 底层数据结构：
+ JDK 7之前的ConCurrentHashMap底层采用的是分段的数组+链表实现，JDK8之后采用的是数组+链表/红黑树；
+ HashTable采用的是数组+链表，数组是主体，链表是解决hash冲突存在的。

### 实现线程安全的方式：
+ JDK8以前，ConCurrentHashMap采用分段锁，对整个数组进行了分段分割，每一把锁只锁容器里的一部分数据，多线程访问不同数据段里的数据，就不会存在锁竞争，提高了并发访问；JDK8以后，直接采用数组+链表/红黑树，并发控制使用CAS和synchronized操作，更加提高了速度。
+ HashTable：所有的方法都加了锁来保证线程安全，但是效率非常的低下，当一个线程访问同步方法，另一个线程也访问的时候，就会陷入阻塞或者轮询的状态。



## 说一下HashMap和HashTable、ConCurrentHashMap的区别
+ HashMap线程不安全，效率高一点，可以存储null的key和value，null的key只能有一个，null的value可以有多个。默认初始容量为16，每次扩充变为原来2倍。创建时如果给定了初始容量，则扩充为2的幂次方大小。底层数据结构为数组+链表，插入元素后如果链表长度大于阈值(默认为8)，先判断数组长度是否小于64，如果小于，则扩充数组，反之将链表转化为红黑树，以减少搜索时间。
+ HashTable线程安全，效率第一点，其内部方法基本都经过synchronized修饰，不可以有null的key和value。默认初始容量为11，每次扩容变为原来的2n+1.创建时给定了初始容量，会直接用给定的大小。底层数据结构为数组+链表。他基本被淘汰了，要保证线程安全可以用ConCurrentHashMap。
+ ConCurrentHashMap是Java中的一个线程安全的哈希表实现，它可以在多线程环境下并发地进行读写操作，而不需要像传统的HashTable那样在读写时加锁。ConCurrentHashMap的实现    


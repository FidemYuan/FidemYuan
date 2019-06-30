---
layout: post
title: concurrentHashMap
categories: JavaSE
tags: 多线程
author: fidemyuan
---

## 什么叫concurrentHashMap

　ConcurrentHashMap是JavaJUC包中提供的一个线程安全且高效的HashMap实现，就是将HashMap包装为一个安全的map。ConcurrentHashMap在并发编程的场景中使用频率非常之高。

## concurrentHashMap的原理

总的来说<br>
利用CAS + synchronized来保证并发更新的安全<br>
底层使用:数组+链表+红黑树来实现<br>

### 重要的变量
1.table：默认为null，初始化发生在第一次插入操作，默认大小为16的数组，用来存储Node节点数据，扩容时大小总是2的幂次方。<br>

2.nextTable：默认为null，扩容时新生成的数组，其大小为原数组的两倍。<br>
3.sizeCtl ：默认为0，用来控制table的初始化和扩容操作，具体应用在后续会体现出来。<br> 
	1 代表table正在初始化 <br>
	N 表示有N-1个线程正在进行扩容操作<br> 
其余情况：<br> 
	(1)如果table未初始化，表示table需要初始化的大小。<br> 
	(2)如果table初始化完成，表示table的容量，默认是table大小的0.75倍，用这个公式算:0.75（n - (n >>> 2)）。<br>

4.Node：保存key，value及key的hash值的数据结构。 
其中value和next都用volatile修饰，保证并发的可见性。



### 实例初始化initTable()

	、private final Node<K,V>[] initTable() {
	    Node<K,V>[] tab; int sc;
	    while ((tab = table) == null || tab.length == 0) {
	//如果一个线程发现sizeCtl<0，意味着另外的线程执行CAS操作成功，当前线程只需要让出cpu时间片
	        if ((sc = sizeCtl) < 0) 
	            Thread.yield(); // lost initialization race; just spin
	        else if (U.compareAndSwapInt(this, SIZECTL, sc, -1)) {
	            try {
	                if ((tab = table) == null || tab.length == 0) {
	                    int n = (sc > 0) ? sc : DEFAULT_CAPACITY;
	                    @SuppressWarnings("unchecked")
	                    Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n];
	                    table = tab = nt;
	                    sc = n - (n >>> 2);  //0.75*capacity
	                }
	            } finally {
	                sizeCtl = sc;
	            }
	            break;
	        }
	    }
	    return tab;
	}、

上面初始化方法，当某线程进来时一开始sc为0，首先静茹else if当中，条件语句就是一个CAS算法：U.compareAndSwapInt(this, SIZECTL, sc, -1)底层是Unsafe类中提供的方法，(Unsafe类似于java后门直接操作底层。)这个方法还是CAS算法，去比较sc与根据this对象的地址偏移量（SIZECTL底层静态代码块拿到可地址偏移量）找到住内存中的值与sc比较，这里是相等的。于是sc=sc-1,sc为-1，<0其他线程进来以后：Thread.yield()线程释放CPU执行权。此时CAS算法返回true进入else if代码块中。<br>

而 sc此时-1不大于0，n=DEFAULT_CAPACITY,DEFAULT_CAPACITY底层已定义为16。<br>
接着Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n];创立节点。<br>
table = tab = nt，把创立的链表数组赋值给table;<br>
sc=(n-(n>>>2));sc为12即阈值；<br>
sizeCtl = sc;<br>

初始化完成。

通过上面CAS加上巧妙的判断操作保证了第一个线程进来以后，就把初始化完成，其他线程进不来保证了线程安全。

###　put方法

通过CAS算法，以及synchronized锁来保证线程安全。<br>

	、final V putVal(K key, V value, boolean onlyIfAbsent) {
	        if (key == null || value == null) throw new NullPointerException();
	        int hash = spread(key.hashCode());
	        int binCount = 0;
	        for (Node<K,V>[] tab = table;;) {
	            Node<K,V> f; int n, i, fh;
	            if (tab == null || (n = tab.length) == 0)
	                tab = initTable();  // lazy Initialization
	            else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {// 当前bucket为空
	                if (casTabAt(tab, i, null,
	                             new Node<K,V>(hash, key, value, null)))
	                    break; // no lock when adding to empty bin
	            }
	         else if ((fh = f.hash) == MOVED)// 当前Map在扩容，先协助扩容,在更新值。
	                tab = helpTransfer(tab, f); 
	            else {  // hash冲突
	                V oldVal = null;
	                synchronized (f) {
	                    if (tabAt(tab, i) == f) { // 链表头节点
	                        if (fh >= 0) {
	                            binCount = 1;
	                            for (Node<K,V> e = f;; ++binCount) {
	                                K ek;
	                            if (e.hash == hash &&//节点已经存在，修改链表节点的值
	                                    ((ek = e.key) == key ||
	                                     (ek != null && key.equals(ek)))) {
	                                    oldVal = e.val;
	                                    if (!onlyIfAbsent)
	                                        e.val = value;
	                                    break;
	                                }
	                                Node<K,V> pred = e;
	                                if ((e = e.next) == null) { // 节点不存在，添加到链表末尾
	                                    pred.next = new Node<K,V>(hash, key,
	                                                              value, null);
	                                    break;
	                                }
	                            }
	                        }
	                        else if (f instanceof TreeBin) { // 红黑树根节点
	                            Node<K,V> p;
	                            binCount = 2;
	                            if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
	                                                           value)) != null) {
	                                oldVal = p.val;
	                                if (!onlyIfAbsent)
	                                    p.val = value;
	                            }
	                        }
	                    }
	                }
	                if (binCount != 0) {
	                    if (binCount >= TREEIFY_THRESHOLD)  //链表节点超过了8，链表转为红黑树
	                        treeifyBin(tab, i);
	                    if (oldVal != null)
	                        return oldVal;
	                    break;
	                }
	            }
	        }
	        addCount(1L, binCount);  // 统计节点个数，检查是否需要resize
	        return null;
	    }  、

上面put方法：<br>
if (tab == null || (n = tab.length) == 0)；//先判断链表数组是否创建好<br>
tab = initTable();  // 没链表数组就先初始化<br>
else if ((f = tabAt(tab, i = (n - 1) & hash)) == null)//如果初始化好以后，先哈希冲突算法：i = (n - 1) & hash)算得对应的索引i，再传入tabAt()方法判断此索引上是否有链表。<br>

if (casTabAt(tab, i, null,new Node<K,V>(hash, key, value, null)))//如果此索引上没值那么就还是进行CAS算法，利用CAS系统原语不可打断，来阻止其他线程进入。再次判断为null然后创建一个节点把key-value值放入。<br>

else{...}//否则，就是此索引上有值的的时候，然后用 synchronized（f）{}锁住此索引上的整个遍历添加节点的操作。<br>

 其中<br>

	、for (Node<K,V> e = f;; ++binCount) {
	         K ek;
	       if (e.hash == hash &&//节点已经存在，修改链表节点的值
	         ((ek = e.key) == key ||
	         (ek != null && key.equals(ek)))) {
	         oldVal = e.val;
	        if (!onlyIfAbsent)
	         e.val = value;
	          break;
	         }
	         Node<K,V> pred = e;
	         if ((e = e.next) == null) { // 节点不存在，添加到链表末尾
	           pred.next = new Node<K,V>(hash, key,
	                                                      value, null);
	             break;
	         }
	                            }、

上面for循环是在遍历此索引上的链表节点，如果有相同的节点，对应value值替换为新值，如果遍历到结尾，没有相同的节点，则在链表末尾创建新的节点放入值。
<br>

## ConcurrentHashMap与HashMap

ConcurrentHashMap是HashMap的高并发版本<br>

## ConcurrentHashMap与HashTable

 Hashtable的任何操作都会把整个表锁住，是阻塞的。好处是总能获取最实时的更新，比如说线程A调用putAll写入大量数据，期间线程B调用get，线程B就会被阻塞，直到线程A完成putAll，因此线程B肯定能获取到线程A写入的完整数据。坏处是所有调用都要排队，效率较低。<br>
ConcurrentHashMap 是设计为非阻塞的。在更新时会局部锁住某部分数据，但不会把整个表都锁住。同步读取操作则是完全非阻塞的。好处是在保证合理的同步前提下，效率很高。坏处 是严格来说读取操作不能保证反映最近的更新。例如线程A调用putAll写入大量数据，期间线程B调用get，则只能get到目前为止已经顺利插入的部分 数据。<br>

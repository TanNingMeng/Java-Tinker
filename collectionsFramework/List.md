## List 

List 有几个特点

1. 有序
2. 每个元素有其对应的索引
3. 允许重复元素
4. 可通过索引访问指定位置的集合元素



以下介绍 List 不同的实现类。



## ArrayList

ArrayList 顾名思义，底层实现是通过 Array 数组实现的。既然是数组，那么肯定有默认的大小。ArrayList 的默认数组大小为 10。<u>当容量不足的时候，ArrayList 会自动进行扩容</u>。扩容的方式是通过 **当前容量 * 扩容系数**来计算的。



ArrayList 

ArrayList 具备以下的方法

- add(E e)
- 

ArrayList 还有其他特点

1. 擅长随机访问
2. 非同步 [非线程安全]
3. 允许 null 元素



## LinkedList

LinkedList 底层是通过**双向链表**实现的。由于数据结构是链表，所以 LinkedList 并不支持随机访问。如果你需要进行访问，那么 LinkedList 会从链表的开头或结尾开始遍历列表。






## 简介

ArrayList 顾名思义，底层实现是通过 Array 数组实现的。既然是数组，那么肯定有默认的大小。ArrayList 的默认数组大小为 10。<u>当容量不足的时候，ArrayList 会自动进行扩容</u>。扩容的方式是通过 **当前容量 * 扩容系数**来计算的。

ArrayList 实现了 AbstractList / List / RandomAccess / Cloneable / Serializable 。

ArrayList 还有其他特点

1. 擅长随机访问
2. 非同步 [非线程安全]
3. 允许 null 元素



## 构造器

```java
    public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }

    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }

    public ArrayList(Collection<? extends E> c) {
        elementData = c.toArray();
        if ((size = elementData.length) != 0) {
            // c.toArray might (incorrectly) not return Object[] (see 6260652)
            if (elementData.getClass() != Object[].class)
                elementData = Arrays.copyOf(elementData, size, Object[].class);
        } else {
            // replace with empty array.
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }
```

ArrayList 有三个构造器

1. 可以创建一个默认容量为 10 的 ArrayList
2. 可以在初始化就赋值一个 Collection 的 ArrayList
3. 可以自定义容量 initialCapacity 大小的 ArrayList



## 基础功能

基础功能指的是 CRUD 增删改查，例如 

- ``添加``： add(E)  / add(int, E) / addAll(Collection) / addAll(int, Collection)
- ``设置``：set(int, E) / 
- ``移除``：remove(int) / remove(E) / fastRemove(E) / removeRange(int, int) / removeAll(Collection) / batchRemove(Collection) / clear()
- ``查找``：contain(Object) / indexOf(Object) / lastIndexOf(Object) / get(int) / 



### 新增

单个添加

```java
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // 校验容量
        elementData[size++] = e;
        return true;
    }

    public void add(int index, E element) {
        rangeCheckForAdd(index);

        ensureCapacityInternal(size + 1);  // 校验容量
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);    // 
        elementData[index] = element;  // 设为 null 这样 GC 就可以检测到了
        size++;
    }
```



### 批量添加

```java
    public boolean addAll(Collection<? extends E> c) {
        Object[] a = c.toArray();
        int numNew = a.length;
        ensureCapacityInternal(size + numNew);  // Increments modCount
        System.arraycopy(a, 0, elementData, size, numNew);
        size += numNew;
        return numNew != 0;
    }
    
    public boolean addAll(int index, Collection<? extends E> c) {
        rangeCheckForAdd(index);

        Object[] a = c.toArray();
        int numNew = a.length;
        ensureCapacityInternal(size + numNew);  // Increments modCount

        int numMoved = size - index;
        if (numMoved > 0)
            System.arraycopy(elementData, index, elementData, index + numNew,
                             numMoved);

        System.arraycopy(a, 0, elementData, index, numNew);
        size += numNew;
        return numNew != 0;
    }
    
    
```



### 删除

删除的逻辑其实没太大好说的。主要的逻辑步骤为：

1. 检查是否越界
2. 



## 扩容与缩容

开始就了，ArrayList 能够自动扩容。那么有扩容，就肯定有缩容。所以我们先将扩容再讲缩容。

### 扩容

扩容一般是在 ArrayList 添加元素的时候进行判断的

```java

		public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }
    
    private void ensureCapacityInternal(int minCapacity) {
        ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
    }
    
  
    private static int calculateCapacity(Object[] elementData, int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            return Math.max(DEFAULT_CAPACITY, minCapacity);
        }
        return minCapacity;
    }
    
    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }

   private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }
```

从代码我们可以看到以下步骤

1. 外部通过 add 添加元素 
2. 调用 ensureCapacityInternal 开始估算内存，同时将 ArrayList 容量 + 1作为参数传进去
3. 进入 calculateCapacity 方法主要是为了计算容器即将的大小。将当前的 elementData 与这次扩容的最小容量 minCapacity 传入。方法里面主要是判断 elementData 是否 ArrayList 默认大小为 10 的数组，如果是的话，就拿默认大小 DEFAULT_CAPACITY 与 minCapacity 进行比较；如果不是的话，直接返回这次的扩容大小 minCapacity
4. 拿到容量大小后，如果比现在的容量大，ArrayList 就需要调用 grow 方法进行扩容。
5. 进入 grow 方法后，首先对旧的容量进行 1.5 倍计算作为 newCapacity。如果 newCapacity 小于 minCapacity，说明 1.5 扩大也不够，那么直接使用  minCapacity；如果 newCapacity 大于 minCapacity，说明 1.5 足够了，那么这时 newCapacity 需要考虑有没有超过 ArrayList 允许的数值。
6. 最后通过 Arrays.copy 创建新数组，大小为 newCapacity 然后将旧数组的元素复制到新数组。



### 缩容

缩容是指当元素变少的时候，ArrayList 会根据情况自动缩小容量，避免资源占用。

```java
    public void trimToSize() {
        modCount++;
        if (size < elementData.length) {
            elementData = (size == 0)
              ? EMPTY_ELEMENTDATA
              : Arrays.copyOf(elementData, size);
        }
    }
```

缩容的逻辑非常简单。当容量等于 0 就直接赋值为 EMPTY_ELEMENTDATA；如果不为 0 就将容量大小恢复到当前数组所拥有元素的数量。



## 迭代器

虽然 ArrayList 实现了 AbstractList，但是为了更好的性能，ArrayList 在迭代器这方面重新实现了 Itr 以及 ListItr。首先我们来说说 Itr 在 ArrayList 下面是怎么优化的。

参数

参数方面没变化，依旧是三个

```java
        int cursor;       // 下一个元素需要被返回的光标
        int lastRet = -1; // 最后一个被返回的元素的下标
        int expectedModCount = modCount;
```




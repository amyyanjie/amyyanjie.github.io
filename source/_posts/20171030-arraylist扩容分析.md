---
title: ArrayList扩容分析
english_title: ArrayList-dilatancy-analysis
date: 2017-10-30 22:36:34
tags: [ArrayList, 集合]
categories: Java
---

ArrayList可以实现容量的自适应的增加(As elements are added to an ArrayList,
its capacity grows automatically)。通过JDK1.8中ArrayList的源码来分析：
## ArrayList的相关定义

ArrayList底层采用Object类型的数组实现。

首先，定义默认初始容量，为10
```
private static final int DEFAULT_CAPACITY = 10;
```
再有，定义用于空实例的空数组实例
```
private static final Object[] EMPTY_ELEMENTDATA = {};
```
再有，定义空数组实例，用于默认大小的空实例
```
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

```

<!--more-->

再有，定义了一个未被序列化的数组elementData，用来存储ArrayList对象列表
```
/**
 * The capacity of the ArrayList is the length of this array buffer.
 */
transient Object[] elementData;
```
再有，定义了ArrayList的size变量
```
/**
     * The size of the ArrayList (the number of elements it contains).
 */
private int size;
```
为了实现ArrayList的自动扩容机制，java引进了capacity和size概念,以区别数组的length。**capacity**是底层数组的长度(length)，**size**是存储的列表对象的数量，被设置为private的。

<!---more-->

## ArrayList的初始化
ArrayList有三种方式来初始化，构造方法源码如下：

**1.用指定的初始容量构造一个空列表**
```
/**
     * Constructs an empty list with the specified initial capacity.
     */
public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            //属性指向新建长度为初始容量的临时数组
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+  initialCapacity);
        }
    }
```
**2.使用初始容量10构造一个空列表**(无参数构造)
```
    /**
     * Constructs an empty list with an initial capacity of ten.
     */
    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
```
**3.构造包含指定collection元素的列表，这些元素利用该集合的迭代器按顺序返回**

如果指定的集合为null，throws NullPointerException。
```
public ArrayList(Collection<? extends E> c) {

        elementData = c.toArray();//用Collection初始化数组elementData
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
## ArrayList扩容
**以无参数构造为例，初始数组容量为10**。当真正对数组进行添加时，才真正分配容量。即向数组中添加第一个元素时，数组容量扩为10。



**1.以add(E e)方法为入口分析**
```
 public boolean add(E e) {
       //添加元素之前，先调用ensureCapacityInternal方法
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;//添加对象时，自增size
        return true;
    }
```
**2.进入到ensureCapacityInternal方法**

ensureCapacityInternal（“确保内部容量”）。
```
 private void ensureCapacityInternal(int minCapacity) {
        //先判断数组是否为空
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
           //如果数组为空，将DEFAULT_CAPACITY(为10)和minCapacity中较大的一个赋值给minCapacity
           //则minCapaciy至少为10
        }
        ensureExplicitCapacity(minCapacity);
        //执行ensureExplicitCapacity方法
    }
```
当空列表add()第1个元素时，调试可知：原本minCapacity为1，在Math.max()方法比较后，minCapacity为10。

但当add()第2个元素时，数组非空，直接进入ensureExplicitCapacity(minCapacity)（此时minCapacity为2）。


**3.进入ensureExplicitCapacity方法**

无论数组是否为空，都会进入ensureExplicitCapacity方法。
```
private void ensureExplicitCapacity(int minCapacity) {
        modCount++;//定义于ArrayList的父类AbstractList，用于存储结构修改次数
        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
        //如果数组的长度(elementData.length)小于最小需要的容量(minCapacity)，就扩容
            grow(minCapacity);
    }
```
当要add第1个元素时，这时elementData.length（为0，因为此时还是空列表）是要比minCapacity（为10）小的，会进入grow(minCapacity)方法（minCapacity为10）。

当add第2个元素时，调试可知：minCapacity为2，此时elementData.length(即是容量)在添加第一个元素后扩容成10了，比minCapacity大，不会去执行grow方法 。数组容量仍为10。

所以当添加第3、4···到第10个元素时，依然不会执行grow方法，数组容量都为10。

直到添加第11个元素，minCapacity(为11)比elementData.length（为10）要大。进入grow方法进行扩容。


**4.进入grow方法进行扩容**
```
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
//用来分配数组的size最大值
private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        //右移一位相当于原数除以2，位运算的计算方式更快
        //所以新容量扩大为原容量的1.5倍
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        //从minCapacity和这个newCapacity中取较大值作为扩容后的新数组长度（新容量）。
        //这主要是针对添加第1个元素。1.5倍oldCapacity为0，所以newCapacity为最小容量10
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity); 
         //如果新容量大于数组的最大size，进入hugeCapacity方法
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
        //最后，将原来数组的数据复制到新的数组中。
    
    }
```
当add第1个元素时，oldCapacity为0，经比较后第一个if判断成立，newCapacity = minCapacity(为10)。但是第二个if判断不会成立，即newCapacity 不比 MAX_ARRAY_SIZE大，则不会进入hugeCapacity方法。数组容量为10，add方法中return true,size增为1。

当add第11个元素进入grow方法时，newCapacity为15，比minCapacity（为11）大，第一个if判断不成立。新容量没有大于数组最大size，不会进入hugeCapacity方法。数组容量扩为15，add方法中return true,size增为11。

以此类推······

**5.如果新容量大于MAX_ARRAY_SIZE,进入hugeCapacity方法**
```
    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
            //对minCapacity和MAX_ARRAY_SIZE进行比较
            //若minCapacity大，将Integer.MAX_VALUE作为新数组的大小
            //若MAX_ARRAY_SIZE大，将MAX_ARRAY_SIZE作为新数组的大小
    }
```

所以，像ArrayList添加对象时，在确定好新数组的大小后，会调用**Arrays.copyOf()方法**，以适当长度(newCapacity)**新建**一个原数组的拷贝，并修改原数组，指向这个新建数组。java垃圾回收机制会自动回收原数组。这样会消耗一定的资源。

因此，在初始化ArrayList时，最好可以估算一个初始大小。

 **7.最好在add大量元素之前用ensureCapacity方法，以减少增量从新分配的次数。** 源码如下：
```
/**
 * <p>An application can increase the capacity of an <tt>ArrayList</tt> instance
 * before adding a large number of elements using the <tt>ensureCapacity</tt>
 * operation.  This may reduce the amount of incremental reallocation.
 */
 
     public void ensureCapacity(int minCapacity) {
        int minExpand = (elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
            // any size if not default element table
            ? 0
            // larger than default for default empty table. It's already
            // supposed to be at default size.
            : DEFAULT_CAPACITY;

        if (minCapacity > minExpand) {
            ensureExplicitCapacity(minCapacity);
        }
    }
```

## 参考：
>[ArrayList自动扩容解析](http://blog.csdn.net/qq_36074157/article/details/69568609)
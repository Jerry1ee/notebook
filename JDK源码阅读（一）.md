# JDK源码阅读

# 集合（一）数组ArrayList

## 1.概述

基于 `[]` 数组实现的，支持**自动扩容**的动态数组。

## 2.类图

![image-20200218094006081](C:\Users\lzy\AppData\Roaming\Typora\typora-user-images\image-20200218094006081.png)

### 实现4个接口

- [`java.util.List`](https://github.com/YunaiV/openjdk/blob/master/src/java.base/share/classes/java/util/List.java) 接口，提供数组的添加、删除、修改、迭代遍历等操作

- [`java.util.RandomAccess`](https://github.com/YunaiV/openjdk/blob/master/src/java.base/share/classes/java/util/RandomAccess.java) 接口，表示 ArrayList 支持**快速**的随机访问
  - RandomAccess是一个空接口，仅作为标志，标志ArrayList可以支持快速随机访问

- [`java.io.Serializable`](https://github.com/YunaiV/openjdk/blob/master/src/java.base/share/classes/java/io/Serializable.java) 接口，表示 ArrayList 支持序列化的功能。

- [`java.lang.Cloneable`](https://github.com/YunaiV/openjdk/blob/master/src/java.base/share/classes/java/lang/Cloneable.java) 接口，表示 ArrayList 支持克隆

### 实现1个抽象类

- 继承了 [`java.util.AbstractList`](https://github.com/YunaiV/openjdk/blob/master/src/java.base/share/classes/java/util/AbstractList.java) 抽象类，而 AbstractList 提供了 List 接口的骨架实现，大幅度的减少了实现**迭代遍历**相关操作的代码

## 3.属性

- `elementData` 属性：元素数组。其中，图中红色空格代表我们已经添加元素，白色空格代表我们并未使用。
- `size` 属性：数组大小。注意，`size` 代表的是 ArrayList 已使用 `elementData` 的元素的数量，对于开发者看到的 `#size()` 也是该大小。并且，当我们添加新的元素时，恰好其就是元素添加到 `elementData` 的位置（下标）。当然，我们知道 ArrayList **真正**的大小是 `elementData` 的大小

![image-20200218101406982](C:\Users\lzy\AppData\Roaming\Typora\typora-user-images\image-20200218101406982.png)

```java
// ArrayList.java

/**
 * 元素数组。
 *
 * 当添加新的元素时，如果该数组不够，会创建新数组，并将原数组的元素拷贝到新数组。之后，将该变量指向新数组。
 *
 * The array buffer into which the elements of the ArrayList are stored.
 * The capacity of the ArrayList is the length of this array buffer. Any
 * empty ArrayList with elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
 * will be expanded to DEFAULT_CAPACITY when the first element is added.
 */
transient Object[] elementData; 
// non-private to simplify nested class access 不使用 private 修复，方便内嵌类的访问。

/**
 * 已使用的数组大小
 *
 * The size of the ArrayList (the number of elements it contains).
 *
 * @serial
 */
private int size;
```

## 4. 构造方法

ArrayList 一共有三个构造方法

**① `#ArrayList(int initialCapacity)`**

`#ArrayList(int initialCapacity)` 构造方法，根据传入的**初始化容量**，创建 ArrayList 数组。如果我们在使用时预先知道数组大小，一定要使用该构造方法，可以避免数组扩容，同时也是合理使用内存。代码如下

```java
// ArrayList.java

/**
 * 共享的空数组对象。
 *
 * 在 {@link #ArrayList(int)} 或 {@link #ArrayList(Collection)} 构造方法中，
 * 如果传入的初始化大小或者集合大小为 0 时，将 {@link #elementData} 指向它。
 *
 * Shared empty array instance used for empty instances.
 */
private static final Object[] EMPTY_ELEMENTDATA = {};

public ArrayList(int initialCapacity) {
    // 初始化容量大于 0 时，创建 Object 数组
    if (initialCapacity > 0) {
        this.elementData = new Object[initialCapacity];
    // 初始化容量等于 0 时，使用 EMPTY_ELEMENTDATA 对象
    } else if (initialCapacity == 0) {
        this.elementData = EMPTY_ELEMENTDATA;
    // 初始化容量小于 0 时，抛出 IllegalArgumentException 异常
    } else {
        throw new IllegalArgumentException("Illegal Capacity: "+
                                           initialCapacity);
    }
}
```

**② `#ArrayList(Collection c)`**

`#ArrayList(Collection c)` 构造方法，使用传入的 `c` 集合，作为 ArrayList 的 `elementData` 。代码如下

```java
// ArrayList.java

public ArrayList(Collection<? extends E> c) {
    // 将 c 转换成 Object 数组
    elementData = c.toArray();
    // 如果数组大小大于 0
    if ((size = elementData.length) != 0) {
        // defend against c.toArray (incorrectly) not returning Object[]
        // (see e.g. https://bugs.openjdk.java.net/browse/JDK-6260652)
        // <X> 如果集合元素不是 Object[] 类型，则会创建新的 Object[] 数组，并将 elementData 赋值到其中，最后赋值给 elementData 。
        if (elementData.getClass() != Object[].class)
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    // 如果数组大小等于 0 ，则使用 EMPTY_ELEMENTDATA 。
    } else {
        // replace with empty array.
        this.elementData = EMPTY_ELEMENTDATA;
    }
}
```

**③ `#ArrayList()`**

无参数构造方法 `#ArrayList()` 构造方法，也是我们使用最多的构造方法

```java
// ArrayList.java

/**
 * 默认初始化容量
 *
 * Default initial capacity.
 */
private static final int DEFAULT_CAPACITY = 10;

/**
 * 共享的空数组对象，用于 {@link #ArrayList()} 构造方法。
 *
 * 在第一次添加元素时，通过使用该静态变量，和 {@link #EMPTY_ELEMENTDATA} 区分开来。
 *
 * Shared empty array instance used for default sized empty instances. We
 * distinguish this from EMPTY_ELEMENTDATA to know how much to inflate when
 * first element is added.
 */
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

/**
 * Constructs an empty list with an initial capacity of ten.
 */
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
```

- ArrayList 默认大小为 10 。但是此处，我们可以看到初始化为 `DEFAULTCAPACITY_EMPTY_ELEMENTDATA` 这个空数组。
- ArrayList 考虑到节省内存，一些使用场景下仅仅是创建了 ArrayList 对象，实际并未使用。所以，ArrayList 优化成初始化是个空数组，在首次添加元素时，才真正初始化为容量为 10 的数组

## 5. 添加单个元素

`#add(E e)` 方法，**顺序**添加单个元素到数组

```java
// ArrayList.java

@Override
public boolean add(E e) {
    // <1> 增加数组修改次数
    modCount++;
    // 添加元素
    add(e, elementData, size);
    // 返回添加成功
    return true;
}

private void add(E e, Object[] elementData, int s) {
    // <2> 如果容量不够，进行扩容
    if (s == elementData.length)
        elementData = grow();
    // <3> 设置到末尾
    elementData[s] = e;
    // <4> 数量大小加一
    size = s + 1;
}
```

- `<1>` 处，增加数组修改次数 `modCount` 。在父类 AbstractList 上，定义了 `modCount` 属性，用于记录数组修改次数。
- `<2>` 处，如果元素添加的位置就超过末尾（数组下标是从 0 开始，而数组大小比最大下标大 1），说明数组容量不够，需要进行扩容，那么就需要调用 `#grow()` 方法，进行扩容
- `<3>` 处，设置到末尾
- `<4>` 处，数量大小加一

## 6. 数组扩容

`#grow()` 方法，扩容数组，并返回它。整个的扩容过程，首先创建一个新的更大的数组，一般是 **1.5 倍**大小（**为什么说是一般呢，稍后我们会看到，会有一些小细节**），然后将原数组复制到新数组中，最后返回新数组

```java
// ArrayList.java

private Object[] grow() {
    // <1>
    return grow(size + 1);
}

private Object[] grow(int minCapacity) {
    int oldCapacity = elementData.length;
    // <2> 如果原容量大于 0 ，或者数组不是 DEFAULTCAPACITY_EMPTY_ELEMENTDATA 时，计算新的数组大小，并创建扩容
    if (oldCapacity > 0 || elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        int newCapacity = ArraysSupport.newLength(oldCapacity,
                minCapacity - oldCapacity, /* minimum growth */
                oldCapacity >> 1           /* preferred growth */);
        return elementData = Arrays.copyOf(elementData, newCapacity);
    // <3> 如果是 DEFAULTCAPACITY_EMPTY_ELEMENTDATA 数组，直接创建新的数组即可。
    } else {
        return elementData = new Object[Math.max(DEFAULT_CAPACITY, minCapacity)];
    }
}
```

- `<1>` 处，调用 `#grow(int minCapacity)` 方法，要求扩容后**至少**比原有大 1 。因为是最小扩容的要求，实际是允许比它大。
- `<2>`处，如果原容量大于 0 时，又或者数组不是 DEFAULTCAPACITY_EMPTY_ELEMENTDATA 时，则计算新的数组大小，并创建扩容。
  - `ArraysSupport#newLength(int oldLength, int minGrowth, int prefGrowth)` 方法，计算新的数组大小。简单来说，结果就是 `Math.max(minGrowth, prefGrowth) + oldLength` ，按照 `minGrowth` 和 `prefGrowth` 取大的。
  - 一般情况下，从 `oldCapacity >> 1` 可以看处，是 **1.5 倍**扩容。但是会有两个特殊情况：
    - 1）初始化数组要求大小为 0 的时候，`0 >> 1` 时（`>> 1 为右移操作，相当于除以 2`）还是 0 ，此时使用 `minCapacity` 传入的 1 。
    - 2）在下文中，我们会看到添加多个元素，此时传入的 `minCapacity` 不再仅仅加 1 ，而是扩容到 `elementData` 数组恰好可以添加下多个元素，而该数量可能会超过当前 ArrayList **0.5** 倍的容量。
- `<3>` 处，如果是 `DEFAULTCAPACITY_EMPTY_ELEMENTDATA` 数组，直接创建新的数组即可。思考下，如果无参构造方法使用 `EMPTY_ELEMENTDATA` 的话，无法实现该效果了

既然有数组扩容方法，那么是否有缩容方法呢？在 `#trimToSize()` 方法中，会创建大小恰好够用的新数组，并将原数组复制到其中。代码如下：

```java
// ArrayList.java

public void trimToSize() {
    // 增加修改次数
    modCount++;
    // 如果有多余的空间，则进行缩容
    if (size < elementData.length) {
        elementData = (size == 0)
          ? EMPTY_ELEMENTDATA // 大小为 0 时，直接使用 EMPTY_ELEMENTDATA
          : Arrays.copyOf(elementData, size); // 大小大于 0 ，则创建大小为 size 的新数组，将原数组复制到其中。
    }
}
```

同时，提供 `#ensureCapacity(int minCapacity)` 方法，保证 `elementData` 数组容量至少有 `minCapacity` 。代码如下：

```java
// ArrayList.java

public void ensureCapacity(int minCapacity) {
    if (minCapacity > elementData.length // 如果 minCapacity 大于数组的容量
        && !(elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
             && minCapacity <= DEFAULT_CAPACITY)) { // 如果 elementData 是 DEFAULTCAPACITY_EMPTY_ELEMENTDATA 的时候，
                                                    // 需要最低 minCapacity 容量大于 DEFAULT_CAPACITY ，因为实际上容量是 DEFAULT_CAPACITY 。
        // 数组修改次数加一
        modCount++;
        // 扩容
        grow(minCapacity);
    }
}
```

## 7. 添加多个元素

`#addAll(Collection c)` 方法，批量添加多个元素。在我们明确知道会添加多个元素时，推荐使用该该方法而不是添加单个元素，避免可能多次扩容。代码如下：

```java
// ArrayList.java

public boolean addAll(Collection<? extends E> c) {
    // 转成 a 数组
    Object[] a = c.toArray();
    // 增加修改次数
    modCount++;
    // 如果 a 数组大小为 0 ，返回 ArrayList 数组无变化
    int numNew = a.length;
    if (numNew == 0)
        return false;
    // <1> 如果 elementData 剩余的空间不够，则进行扩容。要求扩容的大小，至于能够装下 a 数组。
    Object[] elementData;
    final int s;
    if (numNew > (elementData = this.elementData).length - (s = size))
        elementData = grow(s + numNew);
    // <2> 将 a 复制到 elementData 从 s 开始位置
    System.arraycopy(a, 0, elementData, s, numNew);
    // 数组大小加 numNew
    size = s + numNew;
    return true;
}
```

- `<1>` 处，如果 `elementData` 剩余的空间不足，则进行扩容。要求扩容的大小，至于能够装下 `a` 数组。当然，在 [「6. 数组扩容」](http://svip.iocoder.cn/JDK/Collection-ArrayList/#) 的小节，我们已经看到，如果要求扩容的空间太小，则扩容 **1.5 倍**
- `<2>` 处，将 `a` 复制到 `elementData` 从 `s` 开始位置

## 8. 移除单个元素

`#remove(int index)` 方法，移除指定位置的元素，并返回该位置的原元素。代码如下：

```
// ArrayList.java

public E remove(int index) {
    // 校验 index 不要超过 size
    Objects.checkIndex(index, size);
    final Object[] es = elementData;

    // 记录该位置的原值
    @SuppressWarnings("unchecked") E oldValue = (E) es[index];
    // <X>快速移除
    fastRemove(es, index);

    // 返回该位置的原值
    return oldValue;
}
```

重点是 `<x>` 处，调用 `#fastRemove(Object[] es, int i)` 方法，快速移除。代码如下

```java
// ArrayList.java

private void fastRemove(Object[] es, int i) {
    // 增加数组修改次数
    modCount++;
    // <Y>如果 i 不是移除最末尾的元素，则将 i + 1 位置的数组往前挪
    final int newSize;
    if ((newSize = size - 1) > i) // -1 的原因是，size 是从 1 开始，而数组下标是从 0 开始。
        System.arraycopy(es, i + 1, es, i, newSize - i);
    // 将新的末尾置为 null ，帮助 GC
    es[size = newSize] = null;
}
```

`#remove(Object o)` 方法，移除首个为 `o` 的元素，并返回是否移除到。代码如下：

```java
// ArrayList.java

public boolean remove(Object o) {
    final Object[] es = elementData;
    final int size = this.size;
    // <Z> 寻找首个为 o 的位置
    int i = 0;
    found: {
        if (o == null) { // o 为 null 的情况
            for (; i < size; i++)
                if (es[i] == null)
                    break found;
        } else { // o 非 null 的情况
            for (; i < size; i++)
                if (o.equals(es[i]))
                    break found;
        }
        // 如果没找到，返回 false
        return false;
    }
    // 快速移除
    fastRemove(es, i);
    // 找到了，返回 true
    return true;
}
```

和 `#remove(int index)` 差不多，就是在 `<z>` 处，改成获得首个为 `o` 的位置，之后就调用 `#fastRemove(Object[] es, int i)` 方法，快速移除即可

## 9. 移除多个元素

我们先来看 `#removeRange(int fromIndex, int toIndex)` 方法，批量移除 `[fromIndex, toIndex)` 的多个元素，注意不包括 `toIndex` 的元素噢。代码如下：

```java
// ArrayList.java

protected void removeRange(int fromIndex, int toIndex) {
    // 范围不正确，抛出 IndexOutOfBoundsException 异常
    if (fromIndex > toIndex) {
        throw new IndexOutOfBoundsException(
                outOfBoundsMsg(fromIndex, toIndex));
    }
    // 增加数组修改次数
    modCount++;
    // <X> 移除 [fromIndex, toIndex) 的多个元素
    shiftTailOverGap(elementData, fromIndex, toIndex);
}

private static String outOfBoundsMsg(int fromIndex, int toIndex) {
    return "From Index: " + fromIndex + " > To Index: " + toIndex;
}
```

`<x>` 处，调用 `#shiftTailOverGap(Object[] es, int lo, int hi)` 方法，移除 `[fromIndex, toIndex)` 的多个元素。代码如下：

```java
// ArrayList.java

private void shiftTailOverGap(Object[] es, int lo, int hi) {
    // 将 es 从 hi 位置开始的元素，移到 lo 位置开始。
    System.arraycopy(es, hi, es, lo, size - hi);
    // 将从 [size - hi + lo, size) 的元素置空，因为已经被挪到前面了。
    for (int to = size, i = (size -= hi - lo); i < to; i++)
        es[i] = null;
}
```

## 10.查找单个元素

`#indexOf(Object o)` 方法，查找首个为指定元素的位置。代码如下：

遍历数组

```java
// ArrayList.java

public int indexOf(Object o) {
    return indexOfRange(o, 0, size);
}

int indexOfRange(Object o, int start, int end) {
    Object[] es = elementData;
    // o 为 null 的情况
    if (o == null) {
        for (int i = start; i < end; i++) {
            if (es[i] == null) {
                return i;
            }
        }
    // o 非 null 的情况
    } else {
        for (int i = start; i < end; i++) {
            if (o.equals(es[i])) {
                return i;
            }
        }
    }
    // 找不到，返回 -1
    return -1;
}
```

而 `#contains(Object o)` 方法，就是基于该方法实现。代码如下：

```
// ArrayList.java

public boolean contains(Object o) {
    return indexOf(o) >= 0;
}
```

有时我们需要查找最后一个为指定元素的位置，所以会使用到 `#lastIndexOf(Object o)` 方法。代码如下：

从后往前遍历 elementData数组

```java
// ArrayList.java

public int lastIndexOf(Object o) {
    return lastIndexOfRange(o, 0, size);
}

int lastIndexOfRange(Object o, int start, int end) {
    Object[] es = elementData;
    // o 为 null 的情况
    if (o == null) {
        for (int i = end - 1; i >= start; i--) { // 倒序
            if (es[i] == null) {
                return i;
            }
        }
    // o 非 null 的情况
    } else {
        for (int i = end - 1; i >= start; i--) { // 倒序
            if (o.equals(es[i])) {
                return i;
            }
        }
    }

    // 找不到，返回 -1
    return -1;
}
```

## 11. 获得指定位置的元素

`#get(int index)` 方法，获得指定位置的元素。代码如下：

```java
// ArrayList.java

public E get(int index) {
    // 校验 index 不要超过 size
    Objects.checkIndex(index, size);
    // 获得 index 位置的元素
    return elementData(index);
}

E elementData(int index) {
    return (E) elementData[index];
}
```

随机访问 `index` 位置的元素，时间复杂度为 O(1) 。

## 12. 设置指定位置的元素

`#set(int index, E element)` 方法，设置指定位置的元素。代码如下：

```java
// ArrayList.java

public E set(int index, E element) {
    // 校验 index 不要超过 size
    Objects.checkIndex(index, size);
    // 获得 index 位置的原元素
    E oldValue = elementData(index);
    // 修改 index 位置为新元素
    elementData[index] = element;
    // 返回 index 位置的原元素
    return oldValue;
}
```

## 13. 转换成数组

`#toArray()` 方法，将 ArrayList 转换成 `[]` 数组。代码如下：

```java
// ArrayList.java

public Object[] toArray() {
    return Arrays.copyOf(elementData, size);
}

// Arrays.java

public static <T> T[] copyOf(T[] original, int newLength) {
    return (T[]) copyOf(original, newLength, original.getClass());
}
```

- 注意，返回的是 `Object[]` 类型噢。

实际场景下，我们可能想要指定 `T` 泛型的数组，那么我们就需要使用到 `#toArray(T[] a)` 方法。代码如下：

```java
// ArrayList.java

public <T> T[] toArray(T[] a) {
    // <1> 如果传入的数组小于 size 大小，则直接复制一个新数组返回
    if (a.length < size)
        // Make a new array of a's runtime type, but my contents:
        return (T[]) Arrays.copyOf(elementData, size, a.getClass());
    // <2> 将 elementData 复制到 a 中
    System.arraycopy(elementData, 0, a, 0, size);
    // <2.1> 如果传入的数组大于 size 大小，则将 size 赋值为 null
    if (a.length > size)
        a[size] = null;
    // <2.2> 返回 a
    return a;
}
```

## 14. 求哈希值

`#hashCode()` 方法，求 ArrayList 的哈希值。代码如下：

```java
// ArrayList.java

public int hashCode() {
    // 获得当前的数组修改次数
    int expectedModCount = modCount;
    // 计算哈希值
    int hash = hashCodeRange(0, size);
    // 如果修改次数发生改变，则抛出 ConcurrentModificationException 异常
    checkForComodification(expectedModCount);
    return hash;
}

int hashCodeRange(int from, int to) {
    final Object[] es = elementData;
    // 如果 to 超过大小，则抛出 ConcurrentModificationException 异常
    if (to > es.length) {
        throw new ConcurrentModificationException();
    }
    // 遍历每个元素，* 31 求哈希。
    int hashCode = 1;
    for (int i = from; i < to; i++) {
        Object e = es[i];
        hashCode = 31 * hashCode + (e == null ? 0 : e.hashCode());
    }
    return hashCode;
}
```

## 15. 判断相等

`#equals(Object o)` 方法，判断是否相等。代码如下：

```java
// ArrayList.java

public boolean equals(Object o) {
    // 如果是自己，直接返回相等
    if (o == this) {
        return true;
    }

    // 如果不为 List 类型，直接不相等
    if (!(o instanceof List)) {
        return false;
    }

    // 获得当前的数组修改次数
    final int expectedModCount = modCount;
    // ArrayList can be subclassed and given arbitrary behavior, but we can
    // still deal with the common case where o is ArrayList precisely
    // <X> 根据不同类型，调用不同比对的方法。主要考虑 ArrayList 可以直接使用其 elementData 属性，性能更优。
    boolean equal = (o.getClass() == ArrayList.class)
        ? equalsArrayList((ArrayList<?>) o)
        : equalsRange((List<?>) o, 0, size);

    // 如果修改次数发生改变，则抛出 ConcurrentModificationException 异常
    checkForComodification(expectedModCount);
    return equal;
}
```

为什么根据类型是否为 ArrayList ，调用了两个不同的方法去比对呢？

- 普通的 List ，我们只能使用 Iterator 进行迭代，
- 相比 ArrayList 的 `elementData` 属性遍历，性能会略低一些

这两个方法的代码如下，已经添加详细注释

```java
// ArrayList.java

boolean equalsRange(List<?> other, int from, int to) {
    // 如果 to 大于 es 大小，说明说明发生改变，抛出 ConcurrentModificationException 异常
    final Object[] es = elementData;
    if (to > es.length) {
        throw new ConcurrentModificationException();
    }
    // 通过迭代器遍历 other ，然后逐个元素对比
    var oit = other.iterator();
    for (; from < to; from++) {
        // 如果 oit 没有下一个，或者元素不相等，返回 false 不匹配
        if (!oit.hasNext() || !Objects.equals(es[from], oit.next())) {
            return false;
        }
    }
    // 通过 oit 是否遍历完。实现大小是否相等的效果。
    return !oit.hasNext();
}

private boolean equalsArrayList(ArrayList<?> other) {
    // 获得 other 数组修改次数
    final int otherModCount = other.modCount;
    final int s = size;
    boolean equal;
    // 判断数组大小是否相等
    if (equal = (s == other.size)) {
        final Object[] otherEs = other.elementData;
        final Object[] es = elementData;
        // 如果 s 大于 es 或者 otherEs 的长度，说明发生改变，抛出 ConcurrentModificationException 异常
        if (s > es.length || s > otherEs.length) {
            throw new ConcurrentModificationException();
        }
        // 遍历，逐个比较每个元素是否相等
        for (int i = 0; i < s; i++) {
            if (!Objects.equals(es[i], otherEs[i])) {
                equal = false;
                break; // 如果不相等，则 break
            }
        }
    }
    // 如果 other 修改次数发生改变，则抛出 ConcurrentModificationException 异常
    other.checkForComodification(otherModCount);
    return equal;
}
```

## 16. 清空数组

`#clear()` 方法，清空数组。代码如下：

```java
// ArrayList.java

public void clear() {
    // 获得当前的数组修改次数
    modCount++;
    // 遍历数组，倒序设置为 null
    final Object[] es = elementData;
    for (int to = size, i = size = 0; i < to; i++)
        es[i] = null;
}
```

## 17. 序列化数组

`#writeObject(java.io.ObjectOutputStream s)` 方法，实现 ArrayList 的序列化。

```java
// ArrayList.java

@java.io.Serial
private void writeObject(java.io.ObjectOutputStream s)
    throws java.io.IOException {
    // Write out element count, and any hidden stuff
    // 获得当前的数组修改次数
    int expectedModCount = modCount;

    // <1> 写入非静态属性、非 transient 属性
    s.defaultWriteObject();

    // Write out size as capacity for behavioral compatibility with clone()
    // <2> 写入 size ，主要为了与 clone 方法的兼容
    s.writeInt(size);

    // Write out all elements in the proper order.
    // <3> 逐个写入 elementData 数组的元素
    for (int i = 0; i < size; i++) {
        s.writeObject(elementData[i]);
    }

    // 如果 other 修改次数发生改变，则抛出 ConcurrentModificationException 异常
    if (modCount != expectedModCount) {
        throw new ConcurrentModificationException();
    }
}
```

- `<1>` 处，调用 `ObjectOutputStream#defaultWriteObject()` 方法，写入非静态属性、非 `transient` 属性
- `<2>` 处，写入 `size` ，主要为了与 clone 方法的兼容
- `<3>` 处，逐个写入 `elementData` 元素的数组。我们回过来看下 `elementData` 的定义，它是一个 `transient` 修饰的属性。为什么呢？因为 `elementData` 数组，并不一定是全满的，而可能是扩容的时候有一定的预留，如果直接序列化，会有很多空间的浪费，所以只序列化从 `[0, size)` 的元素，减少空间的占用。

## 18. 反序列化数组

`#readObject(java.io.ObjectInputStream s)` 方法，反序列化数组。代码如下：

```java
// ArrayList.java

@java.io.Serial
private void readObject(java.io.ObjectInputStream s)
    throws java.io.IOException, ClassNotFoundException {

    // Read in size, and any hidden stuff
    // 读取非静态属性、非 transient 属性
    s.defaultReadObject();

    // Read in capacity
    // 读取 size ，不过忽略不用
    s.readInt(); // ignored

    if (size > 0) {
        // like clone(), allocate array based upon size not capacity
        SharedSecrets.getJavaObjectInputStreamAccess().checkArray(s, Object[].class, size); // 不知道作甚，哈哈哈。
        // 创建 elements 数组
        Object[] elements = new Object[size];

        // Read in all elements in the proper order.
        // 逐个读取
        for (int i = 0; i < size; i++) {
            elements[i] = s.readObject();
        }

        // 赋值给 elementData
        elementData = elements;
    } else if (size == 0) {
        // 如果 size 是 0 ，则直接使用空数组
        elementData = EMPTY_ELEMENTDATA;
    } else {
        throw new java.io.InvalidObjectException("Invalid size: " + size);
    }
}
```

## 19. 克隆

`#clone()` 方法，克隆 ArrayList 对象。代码如下：

```java
// ArrayList.java

public Object clone() {
    try {
        // 调用父类，进行克隆
        ArrayList<?> v = (ArrayList<?>) super.clone();
        // 拷贝一个新的数组
        v.elementData = Arrays.copyOf(elementData, size);
        // 设置数组修改次数为 0
        v.modCount = 0;
        return v;
    } catch (CloneNotSupportedException e) {
        // this shouldn't happen, since we are Cloneable
        throw new InternalError(e);
    }
}
```

注意，`elementData` 是重新拷贝出来的新的数组，避免和原数组共享

## 20. 创建子数组

`#subList(int fromIndex, int toIndex)` 方法，创建 ArrayList 的子数组。代码如下：

```java
// ArrayList.java

public List<E> subList(int fromIndex, int toIndex) {
    subListRangeCheck(fromIndex, toIndex, size);
    return new SubList<>(this, fromIndex, toIndex);
}

private static class SubList<E> extends AbstractList<E> implements RandomAccess {

    /**
     * 根 ArrayList
     */
    private final ArrayList<E> root;
    /**
     *  父 SubList
     */
    private final SubList<E> parent;
    /**
     * 起始位置
     */
    private final int offset;
    /**
     * 大小
     */
    private int size;

    // ... 省略代码
}
```

**实际使用时，一定要注意，SubList 不是一个只读数组，而是和根数组 `root` 共享相同的 `elementData` 数组，只是说限制了 `[fromIndex, toIndex)` 的范围**

## 21. 创建 Iterator 迭代器

`#iterator()` 方法，创建迭代器。一般情况下，我们使用迭代器遍历 ArrayList、LinkedList 等等 List 的实现类。代码如下

```java
// ArrayList.java

public Iterator<E> iterator() {
    return new Itr();
}
```

- 创建 Itr 迭代器。Itr 实现 [`java.util.Iterator`](https://github.com/YunaiV/openjdk/blob/master/src/java.base/share/classes/java/util/List.java) 接口，是 ArrayList 的内部类。虽然说 AbstractList 也提供了一个 Itr 的实现，但是 ArrayList 为了更好的性能，所以自己实现了，在其类上也有注释“An optimized version of AbstractList.Itr”

Itr 一共有 3 个属性，如下：

```java
// ArrayList.java#Itr

/**
 * 下一个访问元素的位置，从下标 0 开始。
 */
int cursor;       // index of next element to return
/**
 * 上一次访问元素的位置。
 *
 * 1. 初始化为 -1 ，表示无上一个访问的元素
 * 2. 遍历到下一个元素时，lastRet 会指向当前元素，而 cursor 会指向下一个元素。这样，如果我们要实现 remove 方法，移除当前元素，就可以实现了。
 * 3. 移除元素时，设置为 -1 ，表示最后访问的元素不存在了，都被移除咧。
 */
int lastRet = -1; // index of last element returned; -1 if no such
/**
 * 创建迭代器时，数组修改次数。
 *
 * 在迭代过程中，如果数组发生了变化，会抛出 ConcurrentModificationException 异常。
 */
int expectedModCount = modCount;

// prevent creating a synthetic constructor
Itr() {}
```

`#hasNext()` 方法，判断是否还可以继续迭代。代码如下

```java
// ArrayList.java#Itr

public boolean hasNext() {
    return cursor != size;
}
```

- `cursor` 如果等于 `size` ，说明已经到数组末尾，无法继续迭代了。

`#next()` 方法，下一个元素。代码如下：

```java
// ArrayList.java#Itr

public E next() {
    // 校验是否数组发生了变化
    checkForComodification();
    // 判断如果超过 size 范围，抛出 NoSuchElementException 异常
    int i = cursor; // <1> i 记录当前 cursor 的位置
    if (i >= size)
        throw new NoSuchElementException();
    // 判断如果超过 elementData 大小，说明可能被修改了，抛出 ConcurrentModificationException 异常
    Object[] elementData = ArrayList.this.elementData;
    if (i >= elementData.length)
        throw new ConcurrentModificationException();
    // <2> cursor 指向下一个位置
    cursor = i + 1;
    // <3> 返回当前位置的元素
    return (E) elementData[lastRet = i]; // <4> 此处，会将 lastRet 指向当前位置
}

final void checkForComodification() {
    if (modCount != expectedModCount)
        throw new ConcurrentModificationException();
}
```

- `<1>` 处，记录当前 `cursor` 的位置。因为我们当前返回的就是要求 `cursor` 位置的元素。
- `<2>` 处，`cursor` 指向下一个位置。
- `<3>` 处，返回当前位置的元素。同时在 `<4>` 处，会将 `lastRet` 指向当前位置。

`#remove()` 方法，移除当前元素。代码如下：

```java
// ArrayList.java#Itr

public void remove() {
    // 如果 lastRet 小于 0 ，说明没有指向任何元素，抛出 IllegalStateException 异常
    if (lastRet < 0)
        throw new IllegalStateException();
    // 校验是否数组发生了变化
    checkForComodification();

    try {
        // <1> 移除 lastRet 位置的元素
        ArrayList.this.remove(lastRet);
        // <2> cursor 指向 lastRet 位置，因为被移了，所以需要后退下
        cursor = lastRet;
        // <3> lastRet 标记为 -1 ，因为当前元素被移除了
        lastRet = -1;
        // <4> 记录新的数组的修改次数
        expectedModCount = modCount;
    } catch (IndexOutOfBoundsException ex) {
        throw new ConcurrentModificationException();
    }
}
```

- `<1>` 处，调用 `#remove(int index)` 方法，移除 `lastRet` 位置的元素。所以，如果要注意，如果移除元素比较前面，会将后面位置的往前挪，即复制，可能比较消耗性能。
- `<2>` 处，`cursor` 指向 `lastRet` 位置，因为被移了，所以需要后退下。
- `<3>` 处，`lastRet` 标记为 `-1` ，因为当前元素被移除了。
- `<4>` 处，记录新的数组的修改次数。因为此处修改了数组，如果不修改下，后续迭代肯定会报错。

`#forEachRemaining(Consumer action)` 方法，消费剩余未迭代的元素。代码如下：

```java
// ArrayList.java#Itr

@Override
public void forEachRemaining(Consumer<? super E> action) {
    // 要求 action 非空
    Objects.requireNonNull(action);
    // 获得当前数组大小
    final int size = ArrayList.this.size;
    // 记录 i 指向 cursor
    int i = cursor;
    if (i < size) {
        // 判断如果超过 elementData 大小，说明可能被修改了，抛出 ConcurrentModificationException 异常
        final Object[] es = elementData;
        if (i >= es.length)
            throw new ConcurrentModificationException();
        // 逐个处理
        for (; i < size && modCount == expectedModCount; i++)
            action.accept(elementAt(es, i));
        // update once at end to reduce heap write traffic
        // 更新 cursor 和 lastRet 的指向
        cursor = i;
        lastRet = i - 1;
        // 校验是否数组发生了变化
        checkForComodification();
    }
}
```

## 22. 创建 ListIterator 迭代器















- ArrayList 是基于 `[]` 数组实现的 List 实现类，支持在数组容量不够时，一般按照 **1.5** 倍**自动**扩容。同时，它支持**手动**扩容、**手动**缩容。

- ArrayList 随机访问时间复杂度是 O(1) ，查找指定元素的**平均**时间复杂度是 O(n) 。

- ArrayList 移除指定位置的元素的最好时间复杂度是 O(1) ，最坏时间复杂度是 O(n) ，平均时间复杂度是 O(n) 。

  > 最好时间复杂度发生在末尾移除的情况。

- ArrayList 移除指定元素的时间复杂度是 O(n) 。

  > 因为首先需要进行查询，然后在使用移除指定位置的元素，无论怎么计算，都需要 O(n) 的时间复杂度。

- ArrayList 添加元素的最好时间复杂度是 O(1) ，最坏时间复杂度是 O(n) ，平均时间复杂度是 O(n) 。

  > 最好时间复杂度发生在末尾添加的情况。

结尾在抛个拓展，在 Redis String 的数据结构，实现方式是类似 Java ArrayList 的方式
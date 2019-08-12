<!-- GFM-TOC -->
* [一、概覽](#一概覽)
    * [Collection](#collection)
    * [Map](#map)
* [二、容器中的設計模式](#二容器中的設計模式)
    * [迭代器模式](#迭代器模式)
    * [適配器模式](#適配器模式)
* [三、源碼分析](#三源碼分析)
    * [ArrayList](#arraylist)
    * [Vector](#vector)
    * [CopyOnWriteArrayList](#copyonwritearraylist)
    * [LinkedList](#linkedlist)
    * [HashMap](#hashmap)
    * [ConcurrentHashMap](#concurrenthashmap)
    * [LinkedHashMap](#linkedhashmap)
    * [WeakHashMap](#weakhashmap)
* [參考資料](#參考資料)
<!-- GFM-TOC -->


# 一、概覽

容器主要包括 Collection 和 Map 兩種，Collection 存儲著對象的集合，而 Map 存儲著鍵值對（兩個對象）的映射表。

## Collection

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/73403d84-d921-49f1-93a9-d8fe050f3497.png" width="800px"> </div><br>

### 1. Set

- TreeSet：基於紅黑樹實現，支持有序性操作，例如根據一個範圍查找元素的操作。但是查找效率不如 HashSet，HashSet 查找的時間複雜度為 O(1)，TreeSet 則為 O(logN)。

- HashSet：基於哈希表實現，支持快速查找，但不支持有序性操作。並且失去了元素的插入順序信息，也就是說使用 Iterator 遍歷 HashSet 得到的結果是不確定的。

- LinkedHashSet：具有 HashSet 的查找效率，且內部使用雙向鏈表維護元素的插入順序。

### 2. List

- ArrayList：基於動態數組實現，支持隨機訪問。

- Vector：和 ArrayList 類似，但它是線程安全的。

- LinkedList：基於雙向鏈表實現，只能順序訪問，但是可以快速地在鏈表中間插入和刪除元素。不僅如此，LinkedList 還可以用作棧、隊列和雙向隊列。

### 3. Queue

- LinkedList：可以用它來實現雙向隊列。

- PriorityQueue：基於堆結構實現，可以用它來實現優先隊列。

## Map

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/774d756b-902a-41a3-a3fd-81ca3ef688dc.png" width="500px"> </div><br>

- TreeMap：基於紅黑樹實現。

- HashMap：基於哈希表實現。

- HashTable：和 HashMap 類似，但它是線程安全的，這意味著同一時刻多個線程可以同時寫入 HashTable 並且不會導致數據不一致。它是遺留類，不應該去使用它。現在可以使用 ConcurrentHashMap 來支持線程安全，並且 ConcurrentHashMap 的效率會更高，因為 ConcurrentHashMap 引入了分段鎖。

- LinkedHashMap：使用雙向鏈表來維護元素的順序，順序為插入順序或者最近最少使用（LRU）順序。


# 二、容器中的設計模式

## 迭代器模式

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/93fb1d38-83f9-464a-a733-67b2e6bfddda.png" width="600px"> </div><br>

Collection 繼承了 Iterable 接口，其中的 iterator() 方法能夠產生一個 Iterator 對象，通過這個對象就可以迭代遍歷 Collection 中的元素。

從 JDK 1.5 之後可以使用 foreach 方法來遍歷實現了 Iterable 接口的聚合對象。

```java
List<String> list = new ArrayList<>();
list.add("a");
list.add("b");
for (String item : list) {
    System.out.println(item);
}
```

## 適配器模式

java.util.Arrays#asList() 可以把數組類型轉換為 List 類型。

```java
@SafeVarargs
public static <T> List<T> asList(T... a)
```

應該注意的是 asList() 的參數為泛型的變長參數，不能使用基本類型數組作為參數，只能使用相應的包裝類型數組。

```java
Integer[] arr = {1, 2, 3};
List list = Arrays.asList(arr);
```

也可以使用以下方式調用 asList()：

```java
List list = Arrays.asList(1, 2, 3);
```

# 三、源碼分析

如果沒有特別說明，以下源碼分析基於 JDK 1.8。

在 IDEA 中 double shift 調出 Search EveryWhere，查找源碼文件，找到之後就可以閱讀源碼。

## ArrayList


### 1. 概覽

因為 ArrayList 是基於數組實現的，所以支持快速隨機訪問。RandomAccess 接口標識著該類支持快速隨機訪問。

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

數組的默認大小為 10。

```java
private static final int DEFAULT_CAPACITY = 10;
```

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/52a7744f-5bce-4ff3-a6f0-8449334d9f3d.png" width="400px"> </div><br>

### 2. 擴容

添加元素時使用 ensureCapacityInternal() 方法來保證容量足夠，如果不夠時，需要使用 grow() 方法進行擴容，新容量的大小為 `oldCapacity + (oldCapacity >> 1)`，也就是舊容量的 1.5 倍。

擴容操作需要調用 `Arrays.copyOf()` 把原數組整個複製到新數組中，這個操作代價很高，因此最好在創建 ArrayList 對象時就指定大概的容量大小，減少擴容操作的次數。

```java
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}

private void ensureCapacityInternal(int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    ensureExplicitCapacity(minCapacity);
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
```

### 3. 刪除元素

需要調用 System.arraycopy() 將 index+1 後面的元素都複製到 index 位置上，該操作的時間複雜度為 O(N)，可以看出 ArrayList 刪除元素的代價是非常高的。

```java
public E remove(int index) {
    rangeCheck(index);
    modCount++;
    E oldValue = elementData(index);
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index, numMoved);
    elementData[--size] = null; // clear to let GC do its work
    return oldValue;
}
```

### 4. Fail-Fast

modCount 用來記錄 ArrayList 結構發生變化的次數。結構發生變化是指添加或者刪除至少一個元素的所有操作，或者是調整內部數組的大小，僅僅只是設置元素的值不算結構發生變化。

在進行序列化或者迭代等操作時，需要比較操作前後 modCount 是否改變，如果改變了需要拋出 ConcurrentModificationException。

```java
private void writeObject(java.io.ObjectOutputStream s)
    throws java.io.IOException{
    // Write out element count, and any hidden stuff
    int expectedModCount = modCount;
    s.defaultWriteObject();

    // Write out size as capacity for behavioural compatibility with clone()
    s.writeInt(size);

    // Write out all elements in the proper order.
    for (int i=0; i<size; i++) {
        s.writeObject(elementData[i]);
    }

    if (modCount != expectedModCount) {
        throw new ConcurrentModificationException();
    }
}
```

### 5. 序列化

ArrayList 基於數組實現，並且具有動態擴容特性，因此保存元素的數組不一定都會被使用，那麼就沒必要全部進行序列化。

保存元素的數組 elementData 使用 transient 修飾，該關鍵字聲明數組默認不會被序列化。

```java
transient Object[] elementData; // non-private to simplify nested class access
```

ArrayList 實現了 writeObject() 和 readObject() 來控制只序列化數組中有元素填充那部分內容。

```java
private void readObject(java.io.ObjectInputStream s)
    throws java.io.IOException, ClassNotFoundException {
    elementData = EMPTY_ELEMENTDATA;

    // Read in size, and any hidden stuff
    s.defaultReadObject();

    // Read in capacity
    s.readInt(); // ignored

    if (size > 0) {
        // be like clone(), allocate array based upon size not capacity
        ensureCapacityInternal(size);

        Object[] a = elementData;
        // Read in all elements in the proper order.
        for (int i=0; i<size; i++) {
            a[i] = s.readObject();
        }
    }
}
```

```java
private void writeObject(java.io.ObjectOutputStream s)
    throws java.io.IOException{
    // Write out element count, and any hidden stuff
    int expectedModCount = modCount;
    s.defaultWriteObject();

    // Write out size as capacity for behavioural compatibility with clone()
    s.writeInt(size);

    // Write out all elements in the proper order.
    for (int i=0; i<size; i++) {
        s.writeObject(elementData[i]);
    }

    if (modCount != expectedModCount) {
        throw new ConcurrentModificationException();
    }
}
```

序列化時需要使用 ObjectOutputStream 的 writeObject() 將對象轉換為字節流並輸出。而 writeObject() 方法在傳入的對象存在 writeObject() 的時候會去反射調用該對象的 writeObject() 來實現序列化。反序列化使用的是 ObjectInputStream 的 readObject() 方法，原理類似。

```java
ArrayList list = new ArrayList();
ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(file));
oos.writeObject(list);
```

## Vector

### 1. 同步

它的實現與 ArrayList 類似，但是使用了 synchronized 進行同步。

```java
public synchronized boolean add(E e) {
    modCount++;
    ensureCapacityHelper(elementCount + 1);
    elementData[elementCount++] = e;
    return true;
}

public synchronized E get(int index) {
    if (index >= elementCount)
        throw new ArrayIndexOutOfBoundsException(index);

    return elementData(index);
}
```

### 2. 與 ArrayList 的比較

- Vector 是同步的，因此開銷就比 ArrayList 要大，訪問速度更慢。最好使用 ArrayList 而不是 Vector，因為同步操作完全可以由程序員自己來控制；
- Vector 每次擴容請求其大小的 2 倍空間，而 ArrayList 是 1.5 倍。

### 3. 替代方案

可以使用 `Collections.synchronizedList();` 得到一個線程安全的 ArrayList。

```java
List<String> list = new ArrayList<>();
List<String> synList = Collections.synchronizedList(list);
```

也可以使用 concurrent 併發包下的 CopyOnWriteArrayList 類。

```java
List<String> list = new CopyOnWriteArrayList<>();
```

## CopyOnWriteArrayList

### 讀寫分離

寫操作在一個複製的數組上進行，讀操作還是在原始數組中進行，讀寫分離，互不影響。

寫操作需要加鎖，防止併發寫入時導致寫入數據丟失。

寫操作結束之後需要把原始數組指向新的複製數組。

```java
public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] elements = getArray();
        int len = elements.length;
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        newElements[len] = e;
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();
    }
}

final void setArray(Object[] a) {
    array = a;
}
```

```java
@SuppressWarnings("unchecked")
private E get(Object[] a, int index) {
    return (E) a[index];
}
```

### 適用場景

CopyOnWriteArrayList 在寫操作的同時允許讀操作，大大提高了讀操作的性能，因此很適合讀多寫少的應用場景。

但是 CopyOnWriteArrayList 有其缺陷：

- 內存佔用：在寫操作時需要複製一個新的數組，使得內存佔用為原來的兩倍左右；
- 數據不一致：讀操作不能讀取實時性的數據，因為部分寫操作的數據還未同步到讀數組中。

所以 CopyOnWriteArrayList 不適合內存敏感以及對實時性要求很高的場景。

## LinkedList

### 1. 概覽

基於雙向鏈表實現，使用 Node 存儲鏈表節點信息。

```java
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;
}
```

每個鏈表存儲了 first 和 last 指針：

```java
transient Node<E> first;
transient Node<E> last;
```

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/c8563120-cb00-4dd6-9213-9d9b337a7f7c.png" width="500px"> </div><br>

### 2. 與 ArrayList 的比較

- ArrayList 基於動態數組實現，LinkedList 基於雙向鏈表實現；
- ArrayList 支持隨機訪問，LinkedList 不支持；
- LinkedList 在任意位置添加刪除元素更快。

## HashMap

為了便於理解，以下源碼分析以 JDK 1.7 為主。

### 1. 存儲結構

內部包含了一個 Entry 類型的數組 table。

```java
transient Entry[] table;
```

Entry 存儲著鍵值對。它包含了四個字段，從 next 字段我們可以看出 Entry 是一個鏈表。即數組中的每個位置被當成一個桶，一個桶存放一個鏈表。HashMap 使用拉鍊法來解決衝突，同一個鏈表中存放哈希值和散列桶取模運算結果相同的 Entry。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/9420a703-1f9d-42ce-808e-bcb82b56483d.png" width="550px"> </div><br>

```java
static class Entry<K,V> implements Map.Entry<K,V> {
    final K key;
    V value;
    Entry<K,V> next;
    int hash;

    Entry(int h, K k, V v, Entry<K,V> n) {
        value = v;
        next = n;
        key = k;
        hash = h;
    }

    public final K getKey() {
        return key;
    }

    public final V getValue() {
        return value;
    }

    public final V setValue(V newValue) {
        V oldValue = value;
        value = newValue;
        return oldValue;
    }

    public final boolean equals(Object o) {
        if (!(o instanceof Map.Entry))
            return false;
        Map.Entry e = (Map.Entry)o;
        Object k1 = getKey();
        Object k2 = e.getKey();
        if (k1 == k2 || (k1 != null && k1.equals(k2))) {
            Object v1 = getValue();
            Object v2 = e.getValue();
            if (v1 == v2 || (v1 != null && v1.equals(v2)))
                return true;
        }
        return false;
    }

    public final int hashCode() {
        return Objects.hashCode(getKey()) ^ Objects.hashCode(getValue());
    }

    public final String toString() {
        return getKey() + "=" + getValue();
    }
}
```

### 2. 拉鍊法的工作原理

```java
HashMap<String, String> map = new HashMap<>();
map.put("K1", "V1");
map.put("K2", "V2");
map.put("K3", "V3");
```

- 新建一個 HashMap，默認大小為 16；
- 插入 &lt;K1,V1> 鍵值對，先計算 K1 的 hashCode 為 115，使用除留餘數法得到所在的桶下標 115%16=3。
- 插入 &lt;K2,V2> 鍵值對，先計算 K2 的 hashCode 為 118，使用除留餘數法得到所在的桶下標 118%16=6。
- 插入 &lt;K3,V3> 鍵值對，先計算 K3 的 hashCode 為 118，使用除留餘數法得到所在的桶下標 118%16=6，插在 &lt;K2,V2> 前面。

應該注意到鏈表的插入是以頭插法方式進行的，例如上面的 &lt;K3,V3> 不是插在 &lt;K2,V2> 後面，而是插入在鏈表頭部。

查找需要分成兩步進行：

- 計算鍵值對所在的桶；
- 在鏈表上順序查找，時間複雜度顯然和鏈表的長度成正比。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/e0870f80-b79e-4542-ae39-7420d4b0d8fe.png" width="550px"> </div><br>

### 3. put 操作

```java
public V put(K key, V value) {
    if (table == EMPTY_TABLE) {
        inflateTable(threshold);
    }
    // 鍵為 null 單獨處理
    if (key == null)
        return putForNullKey(value);
    int hash = hash(key);
    // 確定桶下標
    int i = indexFor(hash, table.length);
    // 先找出是否已經存在鍵為 key 的鍵值對，如果存在的話就更新這個鍵值對的值為 value
    for (Entry<K,V> e = table[i]; e != null; e = e.next) {
        Object k;
        if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
            V oldValue = e.value;
            e.value = value;
            e.recordAccess(this);
            return oldValue;
        }
    }

    modCount++;
    // 插入新鍵值對
    addEntry(hash, key, value, i);
    return null;
}
```

HashMap 允許插入鍵為 null 的鍵值對。但是因為無法調用 null 的 hashCode() 方法，也就無法確定該鍵值對的桶下標，只能通過強制指定一個桶下標來存放。HashMap 使用第 0 個桶存放鍵為 null 的鍵值對。

```java
private V putForNullKey(V value) {
    for (Entry<K,V> e = table[0]; e != null; e = e.next) {
        if (e.key == null) {
            V oldValue = e.value;
            e.value = value;
            e.recordAccess(this);
            return oldValue;
        }
    }
    modCount++;
    addEntry(0, null, value, 0);
    return null;
}
```

使用鏈表的頭插法，也就是新的鍵值對插在鏈表的頭部，而不是鏈表的尾部。

```java
void addEntry(int hash, K key, V value, int bucketIndex) {
    if ((size >= threshold) && (null != table[bucketIndex])) {
        resize(2 * table.length);
        hash = (null != key) ? hash(key) : 0;
        bucketIndex = indexFor(hash, table.length);
    }

    createEntry(hash, key, value, bucketIndex);
}

void createEntry(int hash, K key, V value, int bucketIndex) {
    Entry<K,V> e = table[bucketIndex];
    // 頭插法，鏈表頭部指向新的鍵值對
    table[bucketIndex] = new Entry<>(hash, key, value, e);
    size++;
}
```

```java
Entry(int h, K k, V v, Entry<K,V> n) {
    value = v;
    next = n;
    key = k;
    hash = h;
}
```

### 4. 確定桶下標

很多操作都需要先確定一個鍵值對所在的桶下標。

```java
int hash = hash(key);
int i = indexFor(hash, table.length);
```

**4.1 計算 hash 值** 

```java
final int hash(Object k) {
    int h = hashSeed;
    if (0 != h && k instanceof String) {
        return sun.misc.Hashing.stringHash32((String) k);
    }

    h ^= k.hashCode();

    // This function ensures that hashCodes that differ only by
    // constant multiples at each bit position have a bounded
    // number of collisions (approximately 8 at default load factor).
    h ^= (h >>> 20) ^ (h >>> 12);
    return h ^ (h >>> 7) ^ (h >>> 4);
}
```

```java
public final int hashCode() {
    return Objects.hashCode(key) ^ Objects.hashCode(value);
}
```

**4.2 取模** 

令 x = 1<<4，即 x 為 2 的 4 次方，它具有以下性質：

```
x   : 00010000
x-1 : 00001111
```

令一個數 y 與 x-1 做與運算，可以去除 y 位級表示的第 4 位以上數：

```
y       : 10110010
x-1     : 00001111
y&(x-1) : 00000010
```

這個性質和 y 對 x 取模效果是一樣的：

```
y   : 10110010
x   : 00010000
y%x : 00000010
```

我們知道，位運算的代價比求模運算小的多，因此在進行這種計算時用位運算的話能帶來更高的性能。

確定桶下標的最後一步是將 key 的 hash 值對桶個數取模：hash%capacity，如果能保證 capacity 為 2 的 n 次方，那麼就可以將這個操作轉換為位運算。

```java
static int indexFor(int h, int length) {
    return h & (length-1);
}
```

### 5. 擴容-基本原理

設 HashMap 的 table 長度為 M，需要存儲的鍵值對數量為 N，如果哈希函數滿足均勻性的要求，那麼每條鏈表的長度大約為 N/M，因此平均查找次數的複雜度為 O(N/M)。

為了讓查找的成本降低，應該儘可能使得 N/M 儘可能小，因此需要保證 M 儘可能大，也就是說 table 要儘可能大。HashMap 採用動態擴容來根據當前的 N 值來調整 M 值，使得空間效率和時間效率都能得到保證。

和擴容相關的參數主要有：capacity、size、threshold 和 load_factor。

| 參數 | 含義 |
| :--: | :-- |
| capacity | table 的容量大小，默認為 16。需要注意的是 capacity 必須保證為 2 的 n 次方。|
| size | 鍵值對數量。 |
| threshold | size 的臨界值，當 size 大於等於 threshold 就必須進行擴容操作。 |
| loadFactor | 裝載因子，table 能夠使用的比例，threshold = capacity * loadFactor。|

```java
static final int DEFAULT_INITIAL_CAPACITY = 16;

static final int MAXIMUM_CAPACITY = 1 << 30;

static final float DEFAULT_LOAD_FACTOR = 0.75f;

transient Entry[] table;

transient int size;

int threshold;

final float loadFactor;

transient int modCount;
```

從下面的添加元素代碼中可以看出，當需要擴容時，令 capacity 為原來的兩倍。

```java
void addEntry(int hash, K key, V value, int bucketIndex) {
    Entry<K,V> e = table[bucketIndex];
    table[bucketIndex] = new Entry<>(hash, key, value, e);
    if (size++ >= threshold)
        resize(2 * table.length);
}
```

擴容使用 resize() 實現，需要注意的是，擴容操作同樣需要把 oldTable 的所有鍵值對重新插入 newTable 中，因此這一步是很費時的。

```java
void resize(int newCapacity) {
    Entry[] oldTable = table;
    int oldCapacity = oldTable.length;
    if (oldCapacity == MAXIMUM_CAPACITY) {
        threshold = Integer.MAX_VALUE;
        return;
    }
    Entry[] newTable = new Entry[newCapacity];
    transfer(newTable);
    table = newTable;
    threshold = (int)(newCapacity * loadFactor);
}

void transfer(Entry[] newTable) {
    Entry[] src = table;
    int newCapacity = newTable.length;
    for (int j = 0; j < src.length; j++) {
        Entry<K,V> e = src[j];
        if (e != null) {
            src[j] = null;
            do {
                Entry<K,V> next = e.next;
                int i = indexFor(e.hash, newCapacity);
                e.next = newTable[i];
                newTable[i] = e;
                e = next;
            } while (e != null);
        }
    }
}
```

### 6. 擴容-重新計算桶下標

在進行擴容時，需要把鍵值對重新放到對應的桶上。HashMap 使用了一個特殊的機制，可以降低重新計算桶下標的操作。

假設原數組長度 capacity 為 16，擴容之後 new capacity 為 32：

```html
capacity     : 00010000
new capacity : 00100000
```

對於一個 Key，

- 它的哈希值如果在第 5 位上為 0，那麼取模得到的結果和之前一樣；
- 如果為 1，那麼得到的結果為原來的結果 +16。

### 7. 計算數組容量

HashMap 構造函數允許用戶傳入的容量不是 2 的 n 次方，因為它可以自動地將傳入的容量轉換為 2 的 n 次方。

先考慮如何求一個數的掩碼，對於 10010000，它的掩碼為 11111111，可以使用以下方法得到：

```
mask |= mask >> 1    11011000
mask |= mask >> 2    11111110
mask |= mask >> 4    11111111
```

mask+1 是大於原始數字的最小的 2 的 n 次方。

```
num     10010000
mask+1 100000000
```

以下是 HashMap 中計算數組容量的代碼：

```java
static final int tableSizeFor(int cap) {
    int n = cap - 1;
    n |= n >>> 1;
    n |= n >>> 2;
    n |= n >>> 4;
    n |= n >>> 8;
    n |= n >>> 16;
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}
```

### 8. 鏈表轉紅黑樹

從 JDK 1.8 開始，一個桶存儲的鏈表長度大於 8 時會將鏈表轉換為紅黑樹。

### 9. 與 HashTable 的比較

- HashTable 使用 synchronized 來進行同步。
- HashMap 可以插入鍵為 null 的 Entry。
- HashMap 的迭代器是 fail-fast 迭代器。
- HashMap 不能保證隨著時間的推移 Map 中的元素次序是不變的。

## ConcurrentHashMap

### 1. 存儲結構

```java
static final class HashEntry<K,V> {
    final int hash;
    final K key;
    volatile V value;
    volatile HashEntry<K,V> next;
}
```

ConcurrentHashMap 和 HashMap 實現上類似，最主要的差別是 ConcurrentHashMap 採用了分段鎖（Segment），每個分段鎖維護著幾個桶（HashEntry），多個線程可以同時訪問不同分段鎖上的桶，從而使其併發度更高（併發度就是 Segment 的個數）。

Segment 繼承自 ReentrantLock。

```java
static final class Segment<K,V> extends ReentrantLock implements Serializable {

    private static final long serialVersionUID = 2249069246763182397L;

    static final int MAX_SCAN_RETRIES =
        Runtime.getRuntime().availableProcessors() > 1 ? 64 : 1;

    transient volatile HashEntry<K,V>[] table;

    transient int count;

    transient int modCount;

    transient int threshold;

    final float loadFactor;
}
```

```java
final Segment<K,V>[] segments;
```

默認的併發級別為 16，也就是說默認創建 16 個 Segment。

```java
static final int DEFAULT_CONCURRENCY_LEVEL = 16;
```

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/db808eff-31d7-4229-a4ad-b8ae71870a3a.png" width="550px"> </div><br>

### 2. size 操作

每個 Segment 維護了一個 count 變量來統計該 Segment 中的鍵值對個數。

```java
/**
 * The number of elements. Accessed only either within locks
 * or among other volatile reads that maintain visibility.
 */
transient int count;
```

在執行 size 操作時，需要遍歷所有 Segment 然後把 count 累計起來。

ConcurrentHashMap 在執行 size 操作時先嚐試不加鎖，如果連續兩次不加鎖操作得到的結果一致，那麼可以認為這個結果是正確的。

嘗試次數使用 RETRIES_BEFORE_LOCK 定義，該值為 2，retries 初始值為 -1，因此嘗試次數為 3。

如果嘗試的次數超過 3 次，就需要對每個 Segment 加鎖。

```java

/**
 * Number of unsynchronized retries in size and containsValue
 * methods before resorting to locking. This is used to avoid
 * unbounded retries if tables undergo continuous modification
 * which would make it impossible to obtain an accurate result.
 */
static final int RETRIES_BEFORE_LOCK = 2;

public int size() {
    // Try a few times to get accurate count. On failure due to
    // continuous async changes in table, resort to locking.
    final Segment<K,V>[] segments = this.segments;
    int size;
    boolean overflow; // true if size overflows 32 bits
    long sum;         // sum of modCounts
    long last = 0L;   // previous sum
    int retries = -1; // first iteration isn't retry
    try {
        for (;;) {
            // 超過嘗試次數，則對每個 Segment 加鎖
            if (retries++ == RETRIES_BEFORE_LOCK) {
                for (int j = 0; j < segments.length; ++j)
                    ensureSegment(j).lock(); // force creation
            }
            sum = 0L;
            size = 0;
            overflow = false;
            for (int j = 0; j < segments.length; ++j) {
                Segment<K,V> seg = segmentAt(segments, j);
                if (seg != null) {
                    sum += seg.modCount;
                    int c = seg.count;
                    if (c < 0 || (size += c) < 0)
                        overflow = true;
                }
            }
            // 連續兩次得到的結果一致，則認為這個結果是正確的
            if (sum == last)
                break;
            last = sum;
        }
    } finally {
        if (retries > RETRIES_BEFORE_LOCK) {
            for (int j = 0; j < segments.length; ++j)
                segmentAt(segments, j).unlock();
        }
    }
    return overflow ? Integer.MAX_VALUE : size;
}
```

### 3. JDK 1.8 的改動

JDK 1.7 使用分段鎖機制來實現併發更新操作，核心類為 Segment，它繼承自重入鎖 ReentrantLock，併發度與 Segment 數量相等。

JDK 1.8 使用了 CAS 操作來支持更高的併發度，在 CAS 操作失敗時使用內置鎖 synchronized。

並且 JDK 1.8 的實現也在鏈表過長時會轉換為紅黑樹。

## LinkedHashMap

### 存儲結構

繼承自 HashMap，因此具有和 HashMap 一樣的快速查找特性。

```java
public class LinkedHashMap<K,V> extends HashMap<K,V> implements Map<K,V>
```

內部維護了一個雙向鏈表，用來維護插入順序或者 LRU 順序。

```java
/**
 * The head (eldest) of the doubly linked list.
 */
transient LinkedHashMap.Entry<K,V> head;

/**
 * The tail (youngest) of the doubly linked list.
 */
transient LinkedHashMap.Entry<K,V> tail;
```

accessOrder 決定了順序，默認為 false，此時維護的是插入順序。

```java
final boolean accessOrder;
```

LinkedHashMap 最重要的是以下用於維護順序的函數，它們會在 put、get 等方法中調用。

```java
void afterNodeAccess(Node<K,V> p) { }
void afterNodeInsertion(boolean evict) { }
```

### afterNodeAccess()

當一個節點被訪問時，如果 accessOrder 為 true，則會將該節點移到鏈表尾部。也就是說指定為 LRU 順序之後，在每次訪問一個節點時，會將這個節點移到鏈表尾部，保證鏈表尾部是最近訪問的節點，那麼鏈表首部就是最近最久未使用的節點。

```java
void afterNodeAccess(Node<K,V> e) { // move node to last
    LinkedHashMap.Entry<K,V> last;
    if (accessOrder && (last = tail) != e) {
        LinkedHashMap.Entry<K,V> p =
            (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
        p.after = null;
        if (b == null)
            head = a;
        else
            b.after = a;
        if (a != null)
            a.before = b;
        else
            last = b;
        if (last == null)
            head = p;
        else {
            p.before = last;
            last.after = p;
        }
        tail = p;
        ++modCount;
    }
}
```

### afterNodeInsertion()

在 put 等操作之後執行，當 removeEldestEntry() 方法返回 true 時會移除最晚的節點，也就是鏈表首部節點 first。

evict 只有在構建 Map 的時候才為 false，在這裡為 true。

```java
void afterNodeInsertion(boolean evict) { // possibly remove eldest
    LinkedHashMap.Entry<K,V> first;
    if (evict && (first = head) != null && removeEldestEntry(first)) {
        K key = first.key;
        removeNode(hash(key), key, null, false, true);
    }
}
```

removeEldestEntry() 默認為 false，如果需要讓它為 true，需要繼承 LinkedHashMap 並且覆蓋這個方法的實現，這在實現 LRU 的緩存中特別有用，通過移除最近最久未使用的節點，從而保證緩存空間足夠，並且緩存的數據都是熱點數據。

```java
protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
    return false;
}
```

### LRU 緩存

以下是使用 LinkedHashMap 實現的一個 LRU 緩存：

- 設定最大緩存空間 MAX_ENTRIES  為 3；
- 使用 LinkedHashMap 的構造函數將 accessOrder 設置為 true，開啟 LRU 順序；
- 覆蓋 removeEldestEntry() 方法實現，在節點多於 MAX_ENTRIES 就會將最近最久未使用的數據移除。

```java
class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private static final int MAX_ENTRIES = 3;

    protected boolean removeEldestEntry(Map.Entry eldest) {
        return size() > MAX_ENTRIES;
    }

    LRUCache() {
        super(MAX_ENTRIES, 0.75f, true);
    }
}
```

```java
public static void main(String[] args) {
    LRUCache<Integer, String> cache = new LRUCache<>();
    cache.put(1, "a");
    cache.put(2, "b");
    cache.put(3, "c");
    cache.get(1);
    cache.put(4, "d");
    System.out.println(cache.keySet());
}
```

```html
[3, 1, 4]
```

## WeakHashMap

### 存儲結構

WeakHashMap 的 Entry 繼承自 WeakReference，被 WeakReference 關聯的對象在下一次垃圾回收時會被回收。

WeakHashMap 主要用來實現緩存，通過使用 WeakHashMap 來引用緩存對象，由 JVM 對這部分緩存進行回收。

```java
private static class Entry<K,V> extends WeakReference<Object> implements Map.Entry<K,V>
```

### ConcurrentCache

Tomcat 中的 ConcurrentCache 使用了 WeakHashMap 來實現緩存功能。

ConcurrentCache 採取的是分代緩存：

- 經常使用的對象放入 eden 中，eden 使用 ConcurrentHashMap 實現，不用擔心會被回收（伊甸園）；
- 不常用的對象放入 longterm，longterm 使用 WeakHashMap 實現，這些老對象會被垃圾收集器回收。
- 當調用  get() 方法時，會先從 eden 區獲取，如果沒有找到的話再到 longterm 獲取，當從 longterm 獲取到就把對象放入 eden 中，從而保證經常被訪問的節點不容易被回收。
- 當調用 put() 方法時，如果 eden 的大小超過了 size，那麼就將 eden 中的所有對象都放入 longterm 中，利用虛擬機回收掉一部分不經常使用的對象。

```java
public final class ConcurrentCache<K, V> {

    private final int size;

    private final Map<K, V> eden;

    private final Map<K, V> longterm;

    public ConcurrentCache(int size) {
        this.size = size;
        this.eden = new ConcurrentHashMap<>(size);
        this.longterm = new WeakHashMap<>(size);
    }

    public V get(K k) {
        V v = this.eden.get(k);
        if (v == null) {
            v = this.longterm.get(k);
            if (v != null)
                this.eden.put(k, v);
        }
        return v;
    }

    public void put(K k, V v) {
        if (this.eden.size() >= size) {
            this.longterm.putAll(this.eden);
            this.eden.clear();
        }
        this.eden.put(k, v);
    }
}
```


# 參考資料

- Eckel B. Java 編程思想 [M]. 機械工業出版社, 2002.
- [Java Collection Framework](https://www.w3resource.com/java-tutorial/java-collections.php)
- [Iterator 模式](https://openhome.cc/Gossip/DesignPattern/IteratorPattern.htm)
- [Java 8 系列之重新認識 HashMap](https://tech.meituan.com/java_hashmap.html)
- [What is difference between HashMap and Hashtable in Java?](http://javarevisited.blogspot.hk/2010/10/difference-between-hashmap-and.html)
- [Java 集合之 HashMap](http://www.zhangchangle.com/2018/02/07/Java%E9%9B%86%E5%90%88%E4%B9%8BHashMap/)
- [The principle of ConcurrentHashMap analysis](http://www.programering.com/a/MDO3QDNwATM.html)
- [探索 ConcurrentHashMap 高併發性的實現機制](https://www.ibm.com/developerworks/cn/java/java-lo-concurrenthashmap/)
- [HashMap 相關面試題及其解答](https://www.jianshu.com/p/75adf47958a7)
- [Java 集合細節（二）：asList 的缺陷](http://wiki.jikexueyuan.com/project/java-enhancement/java-thirtysix.html)
- [Java Collection Framework – The LinkedList Class](http://javaconceptoftheday.com/java-collection-framework-linkedlist-class/)





# 微信公眾號


更多精彩內容將發佈在微信公眾號 CyC2018 上，你也可以在公眾號後臺和我交流學習和求職相關的問題。另外，公眾號提供了該項目的 PDF 等離線閱讀版本，後臺回覆 "下載" 即可領取。公眾號也提供了一份技術面試複習大綱，不僅系統整理了面試知識點，而且標註了各個知識點的重要程度，從而幫你理清多而雜的面試知識點，後臺回覆 "大綱" 即可領取。我基本是按照這個大綱來進行復習的，對我拿到了 BAT 頭條等 Offer 起到很大的幫助。你們完全可以和我一樣根據大綱上列的知識點來進行復習，就不用看很多不重要的內容，也可以知道哪些內容很重要從而多安排一些複習時間。


<br><div align="center"><img width="320px" src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/other/公眾號海報6.png"></img></div>

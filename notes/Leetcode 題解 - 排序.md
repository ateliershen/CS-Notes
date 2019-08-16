<!-- GFM-TOC -->
* [快速選擇](#快速選擇)
* [堆](#堆)
    * [1. Kth Element](#1-kth-element)
* [桶排序](#桶排序)
    * [1. 出現頻率最多的 k 個元素](#1-出現頻率最多的-k-個元素)
    * [2. 按照字符出現次數對字符串排序](#2-按照字符出現次數對字符串排序)
* [荷蘭國旗問題](#荷蘭國旗問題)
    * [1. 按顏色進行排序](#1-按顏色進行排序)
<!-- GFM-TOC -->


# 快速選擇

用於求解  **Kth Element**  問題，也就是第 K 個元素的問題。

可以使用快速排序的 partition() 進行實現。需要先打亂數組，否則最壞情況下時間複雜度為 O(N<sup>2</sup>)。

# 堆

用於求解  **TopK Elements**  問題，也就是 K 個最小元素的問題。可以維護一個大小為 K 的最小堆，最小堆中的元素就是最小元素。最小堆需要使用大頂堆來實現，大頂堆表示堆頂元素是堆中最大元素。這是因為我們要得到 k 個最小的元素，因此當遍歷到一個新的元素時，需要知道這個新元素是否比堆中最大的元素更小，更小的話就把堆中最大元素去除，並將新元素添加到堆中。所以我們需要很容易得到最大元素並移除最大元素，大頂堆就能很好滿足這個要求。

堆也可以用於求解 Kth Element 問題，得到了大小為 k 的最小堆之後，因為使用了大頂堆來實現，因此堆頂元素就是第 k 大的元素。

快速選擇也可以求解 TopK Elements 問題，因為找到 Kth Element 之後，再遍歷一次數組，所有小於等於 Kth Element 的元素都是 TopK Elements。

可以看到，快速選擇和堆排序都可以求解 Kth Element 和 TopK Elements 問題。

## 1. Kth Element

[215. Kth Largest Element in an Array (Medium)](https://leetcode.com/problems/kth-largest-element-in-an-array/description/)

```text
Input: [3,2,1,5,6,4] and k = 2
Output: 5
```

題目描述：找到倒數第 k 個的元素。

**排序** ：時間複雜度 O(NlogN)，空間複雜度 O(1)

```java
public int findKthLargest(int[] nums, int k) {
    Arrays.sort(nums);
    return nums[nums.length - k];
}
```

**堆** ：時間複雜度 O(NlogK)，空間複雜度 O(K)。

```java
public int findKthLargest(int[] nums, int k) {
    PriorityQueue<Integer> pq = new PriorityQueue<>(); // 小頂堆
    for (int val : nums) {
        pq.add(val);
        if (pq.size() > k)  // 維護堆的大小為 K
            pq.poll();
    }
    return pq.peek();
}
```

**快速選擇** ：時間複雜度 O(N)，空間複雜度 O(1)

```java
public int findKthLargest(int[] nums, int k) {
    k = nums.length - k;
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int j = partition(nums, l, h);
        if (j == k) {
            break;
        } else if (j < k) {
            l = j + 1;
        } else {
            h = j - 1;
        }
    }
    return nums[k];
}

private int partition(int[] a, int l, int h) {
    int i = l, j = h + 1;
    while (true) {
        while (a[++i] < a[l] && i < h) ;
        while (a[--j] > a[l] && j > l) ;
        if (i >= j) {
            break;
        }
        swap(a, i, j);
    }
    swap(a, l, j);
    return j;
}

private void swap(int[] a, int i, int j) {
    int t = a[i];
    a[i] = a[j];
    a[j] = t;
}
```

# 桶排序

## 1. 出現頻率最多的 k 個元素

[347. Top K Frequent Elements (Medium)](https://leetcode.com/problems/top-k-frequent-elements/description/)

```html
Given [1,1,1,2,2,3] and k = 2, return [1,2].
```

設置若干個桶，每個桶存儲出現頻率相同的數。桶的下標表示數出現的頻率，即第 i 個桶中存儲的數出現的頻率為 i。

把數都放到桶之後，從後向前遍歷桶，最先得到的 k 個數就是出現頻率最多的的 k 個數。

```java
public List<Integer> topKFrequent(int[] nums, int k) {
    Map<Integer, Integer> frequencyForNum = new HashMap<>();
    for (int num : nums) {
        frequencyForNum.put(num, frequencyForNum.getOrDefault(num, 0) + 1);
    }
    List<Integer>[] buckets = new ArrayList[nums.length + 1];
    for (int key : frequencyForNum.keySet()) {
        int frequency = frequencyForNum.get(key);
        if (buckets[frequency] == null) {
            buckets[frequency] = new ArrayList<>();
        }
        buckets[frequency].add(key);
    }
    List<Integer> topK = new ArrayList<>();
    for (int i = buckets.length - 1; i >= 0 && topK.size() < k; i--) {
        if (buckets[i] == null) {
            continue;
        }
        if (buckets[i].size() <= (k - topK.size())) {
            topK.addAll(buckets[i]);
        } else {
            topK.addAll(buckets[i].subList(0, k - topK.size()));
        }
    }
    return topK;
}
```

## 2. 按照字符出現次數對字符串排序

[451. Sort Characters By Frequency (Medium)](https://leetcode.com/problems/sort-characters-by-frequency/description/)

```html
Input:
"tree"

Output:
"eert"

Explanation:
'e' appears twice while 'r' and 't' both appear once.
So 'e' must appear before both 'r' and 't'. Therefore "eetr" is also a valid answer.
```

```java
public String frequencySort(String s) {
    Map<Character, Integer> frequencyForNum = new HashMap<>();
    for (char c : s.toCharArray())
        frequencyForNum.put(c, frequencyForNum.getOrDefault(c, 0) + 1);

    List<Character>[] frequencyBucket = new ArrayList[s.length() + 1];
    for (char c : frequencyForNum.keySet()) {
        int f = frequencyForNum.get(c);
        if (frequencyBucket[f] == null) {
            frequencyBucket[f] = new ArrayList<>();
        }
        frequencyBucket[f].add(c);
    }
    StringBuilder str = new StringBuilder();
    for (int i = frequencyBucket.length - 1; i >= 0; i--) {
        if (frequencyBucket[i] == null) {
            continue;
        }
        for (char c : frequencyBucket[i]) {
            for (int j = 0; j < i; j++) {
                str.append(c);
            }
        }
    }
    return str.toString();
}
```

# 荷蘭國旗問題

荷蘭國旗包含三種顏色：紅、白、藍。

有三種顏色的球，算法的目標是將這三種球按顏色順序正確地排列。它其實是三向切分快速排序的一種變種，在三向切分快速排序中，每次切分都將數組分成三個區間：小於切分元素、等於切分元素、大於切分元素，而該算法是將數組分成三個區間：等於紅色、等於白色、等於藍色。

<div align="center"> <img src="pics/7a3215ec-6fb7-4935-8b0d-cb408208f7cb.png"/> </div><br>


## 1. 按顏色進行排序

[75. Sort Colors (Medium)](https://leetcode.com/problems/sort-colors/description/)

```html
Input: [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]
```

題目描述：只有 0/1/2 三種顏色。

```java
public void sortColors(int[] nums) {
    int zero = -1, one = 0, two = nums.length;
    while (one < two) {
        if (nums[one] == 0) {
            swap(nums, ++zero, one++);
        } else if (nums[one] == 2) {
            swap(nums, --two, one);
        } else {
            ++one;
        }
    }
}

private void swap(int[] nums, int i, int j) {
    int t = nums[i];
    nums[i] = nums[j];
    nums[j] = t;
}
```




# 微信公眾號


更多精彩內容將發佈在微信公眾號 CyC2018 上，你也可以在公眾號後臺和我交流學習和求職相關的問題。另外，公眾號提供了該項目的 PDF 等離線閱讀版本，後臺回覆 "下載" 即可領取。公眾號也提供了一份技術面試複習大綱，不僅系統整理了面試知識點，而且標註了各個知識點的重要程度，從而幫你理清多而雜的面試知識點，後臺回覆 "大綱" 即可領取。我基本是按照這個大綱來進行復習的，對我拿到了 BAT 頭條等 Offer 起到很大的幫助。你們完全可以和我一樣根據大綱上列的知識點來進行復習，就不用看很多不重要的內容，也可以知道哪些內容很重要從而多安排一些複習時間。


<br><div align="center"><img width="320px" src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/other/公眾號海報6.png"></img></div>

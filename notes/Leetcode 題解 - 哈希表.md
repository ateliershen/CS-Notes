<!-- GFM-TOC -->
* [1. 數組中兩個數的和為給定值](#1-數組中兩個數的和為給定值)
* [2. 判斷數組是否含有重複元素](#2-判斷數組是否含有重複元素)
* [3. 最長和諧序列](#3-最長和諧序列)
* [4. 最長連續序列](#4-最長連續序列)
<!-- GFM-TOC -->


哈希表使用 O(N) 空間複雜度存儲數據，並且以 O(1) 時間複雜度求解問題。

- Java 中的  **HashSet**  用於存儲一個集合，可以查找元素是否在集合中。如果元素有窮，並且範圍不大，那麼可以用一個布爾數組來存儲一個元素是否存在。例如對於只有小寫字符的元素，就可以用一個長度為 26 的布爾數組來存儲一個字符集合，使得空間複雜度降低為 O(1)。

- Java 中的  **HashMap**  主要用於映射關係，從而把兩個元素聯繫起來。HashMap 也可以用來對元素進行計數統計，此時鍵為元素，值為計數。和 HashSet 類似，如果元素有窮並且範圍不大，可以用整型數組來進行統計。在對一個內容進行壓縮或者其它轉換時，利用 HashMap 可以把原始內容和轉換後的內容聯繫起來。例如在一個簡化 url 的系統中 [Leetcdoe : 535. Encode and Decode TinyURL (Medium)](https://leetcode.com/problems/encode-and-decode-tinyurl/description/)，利用 HashMap 就可以存儲精簡後的 url 到原始 url 的映射，使得不僅可以顯示簡化的 url，也可以根據簡化的 url 得到原始 url 從而定位到正確的資源。


# 1. 數組中兩個數的和為給定值

[1. Two Sum (Easy)](https://leetcode.com/problems/two-sum/description/)

可以先對數組進行排序，然後使用雙指針方法或者二分查找方法。這樣做的時間複雜度為 O(NlogN)，空間複雜度為 O(1)。

用 HashMap 存儲數組元素和索引的映射，在訪問到 nums[i] 時，判斷 HashMap 中是否存在 target - nums[i]，如果存在說明 target - nums[i] 所在的索引和 i 就是要找的兩個數。該方法的時間複雜度為 O(N)，空間複雜度為 O(N)，使用空間來換取時間。

```java
public int[] twoSum(int[] nums, int target) {
    HashMap<Integer, Integer> indexForNum = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        if (indexForNum.containsKey(target - nums[i])) {
            return new int[]{indexForNum.get(target - nums[i]), i};
        } else {
            indexForNum.put(nums[i], i);
        }
    }
    return null;
}
```

# 2. 判斷數組是否含有重複元素

[217. Contains Duplicate (Easy)](https://leetcode.com/problems/contains-duplicate/description/)

```java
public boolean containsDuplicate(int[] nums) {
    Set<Integer> set = new HashSet<>();
    for (int num : nums) {
        set.add(num);
    }
    return set.size() < nums.length;
}
```

# 3. 最長和諧序列

[594. Longest Harmonious Subsequence (Easy)](https://leetcode.com/problems/longest-harmonious-subsequence/description/)

```html
Input: [1,3,2,2,5,2,3,7]
Output: 5
Explanation: The longest harmonious subsequence is [3,2,2,2,3].
```

和諧序列中最大數和最小數之差正好為 1，應該注意的是序列的元素不一定是數組的連續元素。

```java
public int findLHS(int[] nums) {
    Map<Integer, Integer> countForNum = new HashMap<>();
    for (int num : nums) {
        countForNum.put(num, countForNum.getOrDefault(num, 0) + 1);
    }
    int longest = 0;
    for (int num : countForNum.keySet()) {
        if (countForNum.containsKey(num + 1)) {
            longest = Math.max(longest, countForNum.get(num + 1) + countForNum.get(num));
        }
    }
    return longest;
}
```

# 4. 最長連續序列

[128. Longest Consecutive Sequence (Hard)](https://leetcode.com/problems/longest-consecutive-sequence/description/)

```html
Given [100, 4, 200, 1, 3, 2],
The longest consecutive elements sequence is [1, 2, 3, 4]. Return its length: 4.
```

要求以 O(N) 的時間複雜度求解。

```java
public int longestConsecutive(int[] nums) {
    Map<Integer, Integer> countForNum = new HashMap<>();
    for (int num : nums) {
        countForNum.put(num, 1);
    }
    for (int num : nums) {
        forward(countForNum, num);
    }
    return maxCount(countForNum);
}

private int forward(Map<Integer, Integer> countForNum, int num) {
    if (!countForNum.containsKey(num)) {
        return 0;
    }
    int cnt = countForNum.get(num);
    if (cnt > 1) {
        return cnt;
    }
    cnt = forward(countForNum, num + 1) + 1;
    countForNum.put(num, cnt);
    return cnt;
}

private int maxCount(Map<Integer, Integer> countForNum) {
    int max = 0;
    for (int num : countForNum.keySet()) {
        max = Math.max(max, countForNum.get(num));
    }
    return max;
}
```




# 微信公眾號


更多精彩內容將發佈在微信公眾號 CyC2018 上，你也可以在公眾號後臺和我交流學習和求職相關的問題。另外，公眾號提供了該項目的 PDF 等離線閱讀版本，後臺回覆 "下載" 即可領取。公眾號也提供了一份技術面試複習大綱，不僅系統整理了面試知識點，而且標註了各個知識點的重要程度，從而幫你理清多而雜的面試知識點，後臺回覆 "大綱" 即可領取。我基本是按照這個大綱來進行復習的，對我拿到了 BAT 頭條等 Offer 起到很大的幫助。你們完全可以和我一樣根據大綱上列的知識點來進行復習，就不用看很多不重要的內容，也可以知道哪些內容很重要從而多安排一些複習時間。


<br><div align="center"><img width="320px" src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/other/公眾號海報6.png"></img></div>

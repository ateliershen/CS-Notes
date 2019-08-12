<!-- GFM-TOC -->
* [1. 求開方](#1-求開方)
* [2. 大於給定元素的最小元素](#2-大於給定元素的最小元素)
* [3. 有序數組的 Single Element](#3-有序數組的-single-element)
* [4. 第一個錯誤的版本](#4-第一個錯誤的版本)
* [5. 旋轉數組的最小數字](#5-旋轉數組的最小數字)
* [6. 查找區間](#6-查找區間)
<!-- GFM-TOC -->


**正常實現** 

```text
Input : [1,2,3,4,5]
key : 3
return the index : 2
```

```java
public int binarySearch(int[] nums, int key) {
    int l = 0, h = nums.length - 1;
    while (l <= h) {
        int m = l + (h - l) / 2;
        if (nums[m] == key) {
            return m;
        } else if (nums[m] > key) {
            h = m - 1;
        } else {
            l = m + 1;
        }
    }
    return -1;
}
```

**時間複雜度** 

二分查找也稱為折半查找，每次都能將查找區間減半，這種折半特性的算法時間複雜度為 O(logN)。

**m 計算** 

有兩種計算中值 m 的方式：

- m = (l + h) / 2
- m = l + (h - l) / 2

l + h 可能出現加法溢出，也就是說加法的結果大於整型能夠表示的範圍。但是 l 和 h 都為正數，因此 h - l 不會出現加法溢出問題。所以，最好使用第二種計算法方法。

**未成功查找的返回值** 

循環退出時如果仍然沒有查找到 key，那麼表示查找失敗。可以有兩種返回值：

- -1：以一個錯誤碼錶示沒有查找到 key
- l：將 key 插入到 nums 中的正確位置

**變種** 

二分查找可以有很多變種，變種實現要注意邊界值的判斷。例如在一個有重複元素的數組中查找 key 的最左位置的實現如下：

```java
public int binarySearch(int[] nums, int key) {
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int m = l + (h - l) / 2;
        if (nums[m] >= key) {
            h = m;
        } else {
            l = m + 1;
        }
    }
    return l;
}
```

該實現和正常實現有以下不同：

- h 的賦值表達式為 h = m
- 循環條件為 l < h
- 最後返回 l 而不是 -1

在 nums[m] >= key 的情況下，可以推導出最左 key 位於 [l, m] 區間中，這是一個閉區間。h 的賦值表達式為 h = m，因為 m 位置也可能是解。

在 h 的賦值表達式為 h = m 的情況下，如果循環條件為 l <= h，那麼會出現循環無法退出的情況，因此循環條件只能是 l < h。以下演示了循環條件為 l <= h 時循環無法退出的情況：

```text
nums = {0, 1, 2}, key = 1
l   m   h
0   1   2  nums[m] >= key
0   0   1  nums[m] < key
1   1   1  nums[m] >= key
1   1   1  nums[m] >= key
...
```

當循環體退出時，不表示沒有查找到 key，因此最後返回的結果不應該為 -1。為了驗證有沒有查找到，需要在調用端判斷一下返回位置上的值和 key 是否相等。

# 1. 求開方

[69. Sqrt(x) (Easy)](https://leetcode.com/problems/sqrtx/description/)

```html
Input: 4
Output: 2

Input: 8
Output: 2
Explanation: The square root of 8 is 2.82842..., and since we want to return an integer, the decimal part will be truncated.
```

一個數 x 的開方 sqrt 一定在 0 \~ x 之間，並且滿足 sqrt == x / sqrt。可以利用二分查找在 0 \~ x 之間查找 sqrt。

對於 x = 8，它的開方是 2.82842...，最後應該返回 2 而不是 3。在循環條件為 l <= h 並且循環退出時，h 總是比 l 小 1，也就是說 h = 2，l = 3，因此最後的返回值應該為 h 而不是 l。

```java
public int mySqrt(int x) {
    if (x <= 1) {
        return x;
    }
    int l = 1, h = x;
    while (l <= h) {
        int mid = l + (h - l) / 2;
        int sqrt = x / mid;
        if (sqrt == mid) {
            return mid;
        } else if (mid > sqrt) {
            h = mid - 1;
        } else {
            l = mid + 1;
        }
    }
    return h;
}
```

# 2. 大於給定元素的最小元素

[744. Find Smallest Letter Greater Than Target (Easy)](https://leetcode.com/problems/find-smallest-letter-greater-than-target/description/)

```html
Input:
letters = ["c", "f", "j"]
target = "d"
Output: "f"

Input:
letters = ["c", "f", "j"]
target = "k"
Output: "c"
```

題目描述：給定一個有序的字符數組 letters 和一個字符 target，要求找出 letters 中大於 target 的最小字符，如果找不到就返回第 1 個字符。

```java
public char nextGreatestLetter(char[] letters, char target) {
    int n = letters.length;
    int l = 0, h = n - 1;
    while (l <= h) {
        int m = l + (h - l) / 2;
        if (letters[m] <= target) {
            l = m + 1;
        } else {
            h = m - 1;
        }
    }
    return l < n ? letters[l] : letters[0];
}
```

# 3. 有序數組的 Single Element

[540. Single Element in a Sorted Array (Medium)](https://leetcode.com/problems/single-element-in-a-sorted-array/description/)

```html
Input: [1, 1, 2, 3, 3, 4, 4, 8, 8]
Output: 2
```

題目描述：一個有序數組只有一個數不出現兩次，找出這個數。

要求以 O(logN) 時間複雜度進行求解，因此不能遍歷數組並進行異或操作來求解，這麼做的時間複雜度為 O(N)。

令 index 為 Single Element 在數組中的位置。在 index 之後，數組中原來存在的成對狀態被改變。如果 m 為偶數，並且 m + 1 < index，那麼 nums[m] == nums[m + 1]；m + 1 >= index，那麼 nums[m] != nums[m + 1]。

從上面的規律可以知道，如果 nums[m] == nums[m + 1]，那麼 index 所在的數組位置為 [m + 2, h]，此時令 l = m + 2；如果 nums[m] != nums[m + 1]，那麼 index 所在的數組位置為 [l, m]，此時令 h = m。

因為 h 的賦值表達式為 h = m，那麼循環條件也就只能使用 l < h 這種形式。

```java
public int singleNonDuplicate(int[] nums) {
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int m = l + (h - l) / 2;
        if (m % 2 == 1) {
            m--;   // 保證 l/h/m 都在偶數位，使得查找區間大小一直都是奇數
        }
        if (nums[m] == nums[m + 1]) {
            l = m + 2;
        } else {
            h = m;
        }
    }
    return nums[l];
}
```

# 4. 第一個錯誤的版本

[278. First Bad Version (Easy)](https://leetcode.com/problems/first-bad-version/description/)

題目描述：給定一個元素 n 代表有 [1, 2, ..., n] 版本，在第 x 位置開始出現錯誤版本，導致後面的版本都錯誤。可以調用 isBadVersion(int x) 知道某個版本是否錯誤，要求找到第一個錯誤的版本。

如果第 m 個版本出錯，則表示第一個錯誤的版本在 [l, m] 之間，令 h = m；否則第一個錯誤的版本在 [m + 1, h] 之間，令 l = m + 1。

因為 h 的賦值表達式為 h = m，因此循環條件為 l < h。

```java
public int firstBadVersion(int n) {
    int l = 1, h = n;
    while (l < h) {
        int mid = l + (h - l) / 2;
        if (isBadVersion(mid)) {
            h = mid;
        } else {
            l = mid + 1;
        }
    }
    return l;
}
```

# 5. 旋轉數組的最小數字

[153. Find Minimum in Rotated Sorted Array (Medium)](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/description/)

```html
Input: [3,4,5,1,2],
Output: 1
```

```java
public int findMin(int[] nums) {
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int m = l + (h - l) / 2;
        if (nums[m] <= nums[h]) {
            h = m;
        } else {
            l = m + 1;
        }
    }
    return nums[l];
}
```

# 6. 查找區間

[34. Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

```html
Input: nums = [5,7,7,8,8,10], target = 8
Output: [3,4]

Input: nums = [5,7,7,8,8,10], target = 6
Output: [-1,-1]
```

```java
public int[] searchRange(int[] nums, int target) {
    int first = binarySearch(nums, target);
    int last = binarySearch(nums, target + 1) - 1;
    if (first == nums.length || nums[first] != target) {
        return new int[]{-1, -1};
    } else {
        return new int[]{first, Math.max(first, last)};
    }
}

private int binarySearch(int[] nums, int target) {
    int l = 0, h = nums.length; // 注意 h 的初始值
    while (l < h) {
        int m = l + (h - l) / 2;
        if (nums[m] >= target) {
            h = m;
        } else {
            l = m + 1;
        }
    }
    return l;
}
```





# 微信公眾號


更多精彩內容將發佈在微信公眾號 CyC2018 上，你也可以在公眾號後臺和我交流學習和求職相關的問題。另外，公眾號提供了該項目的 PDF 等離線閱讀版本，後臺回覆 "下載" 即可領取。公眾號也提供了一份技術面試複習大綱，不僅系統整理了面試知識點，而且標註了各個知識點的重要程度，從而幫你理清多而雜的面試知識點，後臺回覆 "大綱" 即可領取。我基本是按照這個大綱來進行復習的，對我拿到了 BAT 頭條等 Offer 起到很大的幫助。你們完全可以和我一樣根據大綱上列的知識點來進行復習，就不用看很多不重要的內容，也可以知道哪些內容很重要從而多安排一些複習時間。


<br><div align="center"><img width="320px" src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/other/公眾號海報6.png"></img></div>

<!-- GFM-TOC -->
* [1. 分配餅乾](#1-分配餅乾)
* [2. 不重疊的區間個數](#2-不重疊的區間個數)
* [3. 投飛鏢刺破氣球](#3-投飛鏢刺破氣球)
* [4. 根據身高和序號重組隊列](#4-根據身高和序號重組隊列)
* [5. 買賣股票最大的收益](#5-買賣股票最大的收益)
* [6. 買賣股票的最大收益 II](#6-買賣股票的最大收益-ii)
* [7. 種植花朵](#7-種植花朵)
* [8. 判斷是否為子序列](#8-判斷是否為子序列)
* [9. 修改一個數成為非遞減數組](#9-修改一個數成為非遞減數組)
* [10. 子數組最大的和](#10-子數組最大的和)
* [11. 分隔字符串使同種字符出現在一起](#11-分隔字符串使同種字符出現在一起)
<!-- GFM-TOC -->


保證每次操作都是局部最優的，並且最後得到的結果是全局最優的。

# 1. 分配餅乾

[455. Assign Cookies (Easy)](https://leetcode.com/problems/assign-cookies/description/)

```html
Input: [1,2], [1,2,3]
Output: 2

Explanation: You have 2 children and 3 cookies. The greed factors of 2 children are 1, 2.
You have 3 cookies and their sizes are big enough to gratify all of the children,
You need to output 2.
```

題目描述：每個孩子都有一個滿足度，每個餅乾都有一個大小，只有餅乾的大小大於等於一個孩子的滿足度，該孩子才會獲得滿足。求解最多可以獲得滿足的孩子數量。

給一個孩子的餅乾應當儘量小又能滿足該孩子，這樣大餅乾就能拿來給滿足度比較大的孩子。因為最小的孩子最容易得到滿足，所以先滿足最小的孩子。

證明：假設在某次選擇中，貪心策略選擇給當前滿足度最小的孩子分配第 m 個餅乾，第 m 個餅乾為可以滿足該孩子的最小餅乾。假設存在一種最優策略，給該孩子分配第 n 個餅乾，並且 m < n。我們可以發現，經過這一輪分配，貪心策略分配後剩下的餅乾一定有一個比最優策略來得大。因此在後續的分配中，貪心策略一定能滿足更多的孩子。也就是說不存在比貪心策略更優的策略，即貪心策略就是最優策略。

```java
public int findContentChildren(int[] g, int[] s) {
    Arrays.sort(g);
    Arrays.sort(s);
    int gi = 0, si = 0;
    while (gi < g.length && si < s.length) {
        if (g[gi] <= s[si]) {
            gi++;
        }
        si++;
    }
    return gi;
}
```

# 2. 不重疊的區間個數

[435. Non-overlapping Intervals (Medium)](https://leetcode.com/problems/non-overlapping-intervals/description/)

```html
Input: [ [1,2], [1,2], [1,2] ]

Output: 2

Explanation: You need to remove two [1,2] to make the rest of intervals non-overlapping.
```

```html
Input: [ [1,2], [2,3] ]

Output: 0

Explanation: You don't need to remove any of the intervals since they're already non-overlapping.
```

題目描述：計算讓一組區間不重疊所需要移除的區間個數。

先計算最多能組成的不重疊區間個數，然後用區間總個數減去不重疊區間的個數。

在每次選擇中，區間的結尾最為重要，選擇的區間結尾越小，留給後面的區間的空間越大，那麼後面能夠選擇的區間個數也就越大。

按區間的結尾進行排序，每次選擇結尾最小，並且和前一個區間不重疊的區間。

```java
public int eraseOverlapIntervals(int[][] intervals) {
    if (intervals.length == 0) {
        return 0;
    }
    Arrays.sort(intervals, Comparator.comparingInt(o -> o[1]));
    int cnt = 1;
    int end = intervals[0][1];
    for (int i = 1; i < intervals.length; i++) {
        if (intervals[i][0] < end) {
            continue;
        }
        end = intervals[i][1];
        cnt++;
    }
    return intervals.length - cnt;
}
```

使用 lambda 表示式創建 Comparator 會導致算法運行時間過長，如果注重運行時間，可以修改為普通創建 Comparator 語句：

```java
Arrays.sort(intervals, new Comparator<int[]>() {
    @Override
    public int compare(int[] o1, int[] o2) {
        return o1[1] - o2[1];
    }
});
```

# 3. 投飛鏢刺破氣球

[452. Minimum Number of Arrows to Burst Balloons (Medium)](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/description/)

```
Input:
[[10,16], [2,8], [1,6], [7,12]]

Output:
2
```

題目描述：氣球在一個水平數軸上擺放，可以重疊，飛鏢垂直投向座標軸，使得路徑上的氣球都被刺破。求解最小的投飛鏢次數使所有氣球都被刺破。

也是計算不重疊的區間個數，不過和 Non-overlapping Intervals 的區別在於，[1, 2] 和 [2, 3] 在本題中算是重疊區間。

```java
public int findMinArrowShots(int[][] points) {
    if (points.length == 0) {
        return 0;
    }
    Arrays.sort(points, Comparator.comparingInt(o -> o[1]));
    int cnt = 1, end = points[0][1];
    for (int i = 1; i < points.length; i++) {
        if (points[i][0] <= end) {
            continue;
        }
        cnt++;
        end = points[i][1];
    }
    return cnt;
}
```

# 4. 根據身高和序號重組隊列

[406. Queue Reconstruction by Height(Medium)](https://leetcode.com/problems/queue-reconstruction-by-height/description/)

```html
Input:
[[7,0], [4,4], [7,1], [5,0], [6,1], [5,2]]

Output:
[[5,0], [7,0], [5,2], [6,1], [4,4], [7,1]]
```

題目描述：一個學生用兩個分量 (h, k) 描述，h 表示身高，k 表示排在前面的有 k 個學生的身高比他高或者和他一樣高。

為了使插入操作不影響後續的操作，身高較高的學生應該先做插入操作，否則身高較小的學生原先正確插入的第 k 個位置可能會變成第 k+1 個位置。

身高 h 降序、個數 k 值升序，然後將某個學生插入隊列的第 k 個位置中。

```java
public int[][] reconstructQueue(int[][] people) {
    if (people == null || people.length == 0 || people[0].length == 0) {
        return new int[0][0];
    }
    Arrays.sort(people, (a, b) -> (a[0] == b[0] ? a[1] - b[1] : b[0] - a[0]));
    List<int[]> queue = new ArrayList<>();
    for (int[] p : people) {
        queue.add(p[1], p);
    }
    return queue.toArray(new int[queue.size()][]);
}
```

# 5. 買賣股票最大的收益

[121. Best Time to Buy and Sell Stock (Easy)](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/)

題目描述：一次股票交易包含買入和賣出，只進行一次交易，求最大收益。

只要記錄前面的最小价格，將這個最小价格作為買入價格，然後將當前的價格作為售出價格，查看當前收益是不是最大收益。

```java
public int maxProfit(int[] prices) {
    int n = prices.length;
    if (n == 0) return 0;
    int soFarMin = prices[0];
    int max = 0;
    for (int i = 1; i < n; i++) {
        if (soFarMin > prices[i]) soFarMin = prices[i];
        else max = Math.max(max, prices[i] - soFarMin);
    }
    return max;
}
```


# 6. 買賣股票的最大收益 II

[122. Best Time to Buy and Sell Stock II (Easy)](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/description/)

題目描述：可以進行多次交易，多次交易之間不能交叉進行，可以進行多次交易。

對於 [a, b, c, d]，如果有 a <= b <= c <= d ，那麼最大收益為 d - a。而 d - a = (d - c) + (c - b) + (b - a) ，因此當訪問到一個 prices[i] 且 prices[i] - prices[i-1] > 0，那麼就把 prices[i] - prices[i-1] 添加到收益中。

```java
public int maxProfit(int[] prices) {
    int profit = 0;
    for (int i = 1; i < prices.length; i++) {
        if (prices[i] > prices[i - 1]) {
            profit += (prices[i] - prices[i - 1]);
        }
    }
    return profit;
}
```


# 7. 種植花朵

[605. Can Place Flowers (Easy)](https://leetcode.com/problems/can-place-flowers/description/)

```html
Input: flowerbed = [1,0,0,0,1], n = 1
Output: True
```

題目描述：flowerbed 數組中 1 表示已經種下了花朵。花朵之間至少需要一個單位的間隔，求解是否能種下 n 朵花。

```java
public boolean canPlaceFlowers(int[] flowerbed, int n) {
    int len = flowerbed.length;
    int cnt = 0;
    for (int i = 0; i < len && cnt < n; i++) {
        if (flowerbed[i] == 1) {
            continue;
        }
        int pre = i == 0 ? 0 : flowerbed[i - 1];
        int next = i == len - 1 ? 0 : flowerbed[i + 1];
        if (pre == 0 && next == 0) {
            cnt++;
            flowerbed[i] = 1;
        }
    }
    return cnt >= n;
}
```

# 8. 判斷是否為子序列

[392. Is Subsequence (Medium)](https://leetcode.com/problems/is-subsequence/description/)

```html
s = "abc", t = "ahbgdc"
Return true.
```

```java
public boolean isSubsequence(String s, String t) {
    int index = -1;
    for (char c : s.toCharArray()) {
        index = t.indexOf(c, index + 1);
        if (index == -1) {
            return false;
        }
    }
    return true;
}
```

# 9. 修改一個數成為非遞減數組

[665. Non-decreasing Array (Easy)](https://leetcode.com/problems/non-decreasing-array/description/)

```html
Input: [4,2,3]
Output: True
Explanation: You could modify the first 4 to 1 to get a non-decreasing array.
```

題目描述：判斷一個數組是否能只修改一個數就成為非遞減數組。

在出現 nums[i] < nums[i - 1] 時，需要考慮的是應該修改數組的哪個數，使得本次修改能使 i 之前的數組成為非遞減數組，並且  **不影響後續的操作** 。優先考慮令 nums[i - 1] = nums[i]，因為如果修改 nums[i] = nums[i - 1] 的話，那麼 nums[i] 這個數會變大，就有可能比 nums[i + 1] 大，從而影響了後續操作。還有一個比較特別的情況就是 nums[i] < nums[i - 2]，修改 nums[i - 1] = nums[i] 不能使數組成為非遞減數組，只能修改 nums[i] = nums[i - 1]。

```java
public boolean checkPossibility(int[] nums) {
    int cnt = 0;
    for (int i = 1; i < nums.length && cnt < 2; i++) {
        if (nums[i] >= nums[i - 1]) {
            continue;
        }
        cnt++;
        if (i - 2 >= 0 && nums[i - 2] > nums[i]) {
            nums[i] = nums[i - 1];
        } else {
            nums[i - 1] = nums[i];
        }
    }
    return cnt <= 1;
}
```



# 10. 子數組最大的和

[53. Maximum Subarray (Easy)](https://leetcode.com/problems/maximum-subarray/description/)

```html
For example, given the array [-2,1,-3,4,-1,2,1,-5,4],
the contiguous subarray [4,-1,2,1] has the largest sum = 6.
```

```java
public int maxSubArray(int[] nums) {
    if (nums == null || nums.length == 0) {
        return 0;
    }
    int preSum = nums[0];
    int maxSum = preSum;
    for (int i = 1; i < nums.length; i++) {
        preSum = preSum > 0 ? preSum + nums[i] : nums[i];
        maxSum = Math.max(maxSum, preSum);
    }
    return maxSum;
}
```

# 11. 分隔字符串使同種字符出現在一起

[763. Partition Labels (Medium)](https://leetcode.com/problems/partition-labels/description/)

```html
Input: S = "ababcbacadefegdehijhklij"
Output: [9,7,8]
Explanation:
The partition is "ababcbaca", "defegde", "hijhklij".
This is a partition so that each letter appears in at most one part.
A partition like "ababcbacadefegde", "hijhklij" is incorrect, because it splits S into less parts.
```

```java
public List<Integer> partitionLabels(String S) {
    int[] lastIndexsOfChar = new int[26];
    for (int i = 0; i < S.length(); i++) {
        lastIndexsOfChar[char2Index(S.charAt(i))] = i;
    }
    List<Integer> partitions = new ArrayList<>();
    int firstIndex = 0;
    while (firstIndex < S.length()) {
        int lastIndex = firstIndex;
        for (int i = firstIndex; i < S.length() && i <= lastIndex; i++) {
            int index = lastIndexsOfChar[char2Index(S.charAt(i))];
            if (index > lastIndex) {
                lastIndex = index;
            }
        }
        partitions.add(lastIndex - firstIndex + 1);
        firstIndex = lastIndex + 1;
    }
    return partitions;
}

private int char2Index(char c) {
    return c - 'a';
}
```




# 微信公眾號


更多精彩內容將發佈在微信公眾號 CyC2018 上，你也可以在公眾號後臺和我交流學習和求職相關的問題。另外，公眾號提供了該項目的 PDF 等離線閱讀版本，後臺回覆 "下載" 即可領取。公眾號也提供了一份技術面試複習大綱，不僅系統整理了面試知識點，而且標註了各個知識點的重要程度，從而幫你理清多而雜的面試知識點，後臺回覆 "大綱" 即可領取。我基本是按照這個大綱來進行復習的，對我拿到了 BAT 頭條等 Offer 起到很大的幫助。你們完全可以和我一樣根據大綱上列的知識點來進行復習，就不用看很多不重要的內容，也可以知道哪些內容很重要從而多安排一些複習時間。


<br><div align="center"><img width="320px" src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/other/公眾號海報6.png"></img></div>

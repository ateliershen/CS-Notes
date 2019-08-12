<!-- GFM-TOC -->
* [3. 數組中重複的數字](#3-數組中重複的數字)
* [4. 二維數組中的查找](#4-二維數組中的查找)
* [5. 替換空格](#5-替換空格)
* [6. 從尾到頭打印鏈表](#6-從尾到頭打印鏈表)
* [7. 重建二叉樹](#7-重建二叉樹)
* [8. 二叉樹的下一個結點](#8-二叉樹的下一個結點)
* [9. 用兩個棧實現隊列](#9-用兩個棧實現隊列)
<!-- GFM-TOC -->


# 3. 數組中重複的數字

[NowCoder](https://www.nowcoder.com/practice/623a5ac0ea5b4e5f95552655361ae0a8?tpId=13&tqId=11203&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 題目描述

在一個長度為 n 的數組裡的所有數字都在 0 到 n-1 的範圍內。數組中某些數字是重複的，但不知道有幾個數字是重複的，也不知道每個數字重複幾次。請找出數組中任意一個重複的數字。

```html
Input:
{2, 3, 1, 0, 2, 5}

Output:
2
```

## 解題思路

要求時間複雜度 O(N)，空間複雜度 O(1)。因此不能使用排序的方法，也不能使用額外的標記數組。

對於這種數組元素在 [0, n-1] 範圍內的問題，可以將值為 i 的元素調整到第 i 個位置上進行求解。

以 (2, 3, 1, 0, 2, 5) 為例，遍歷到位置 4 時，該位置上的數為 2，但是第 2 個位置上已經有一個 2 的值了，因此可以知道 2 重複：

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/49d2adc1-b28a-44bf-babb-d44993f4a2e3.gif" width="250px"> </div><br>


```java
public boolean duplicate(int[] nums, int length, int[] duplication) {
    if (nums == null || length <= 0)
        return false;
    for (int i = 0; i < length; i++) {
        while (nums[i] != i) {
            if (nums[i] == nums[nums[i]]) {
                duplication[0] = nums[i];
                return true;
            }
            swap(nums, i, nums[i]);
        }
    }
    return false;
}

private void swap(int[] nums, int i, int j) {
    int t = nums[i];
    nums[i] = nums[j];
    nums[j] = t;
}
```

# 4. 二維數組中的查找

[NowCoder](https://www.nowcoder.com/practice/abc3fe2ce8e146608e868a70efebf62e?tpId=13&tqId=11154&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 題目描述

給定一個二維數組，其每一行從左到右遞增排序，從上到下也是遞增排序。給定一個數，判斷這個數是否在該二維數組中。

```html
Consider the following matrix:
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]

Given target = 5, return true.
Given target = 20, return false.
```

## 解題思路

要求時間複雜度 O(M + N)，空間複雜度 O(1)。其中 M 為行數，N 為 列數。

該二維數組中的一個數，小於它的數一定在其左邊，大於它的數一定在其下邊。因此，從右上角開始查找，就可以根據 target 和當前元素的大小關係來縮小查找區間，當前元素的查找區間為左下角的所有元素。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/0ad9f7ba-f408-4999-a77a-9b73562c9088.gif" width="200px"> </div><br>

```java
public boolean Find(int target, int[][] matrix) {
    if (matrix == null || matrix.length == 0 || matrix[0].length == 0)
        return false;
    int rows = matrix.length, cols = matrix[0].length;
    int r = 0, c = cols - 1; // 從右上角開始
    while (r <= rows - 1 && c >= 0) {
        if (target == matrix[r][c])
            return true;
        else if (target > matrix[r][c])
            r++;
        else
            c--;
    }
    return false;
}
```

# 5. 替換空格

[NowCoder](https://www.nowcoder.com/practice/4060ac7e3e404ad1a894ef3e17650423?tpId=13&tqId=11155&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 題目描述


將一個字符串中的空格替換成 "%20"。

```text
Input:
"A B"

Output:
"A%20B"
```

## 解題思路

在字符串尾部填充任意字符，使得字符串的長度等於替換之後的長度。因為一個空格要替換成三個字符（%20），因此當遍歷到一個空格時，需要在尾部填充兩個任意字符。

令 P1 指向字符串原來的末尾位置，P2 指向字符串現在的末尾位置。P1 和 P2 從後向前遍歷，當 P1 遍歷到一個空格時，就需要令 P2 指向的位置依次填充 02%（注意是逆序的），否則就填充上 P1 指向字符的值。

從後向前遍是為了在改變 P2 所指向的內容時，不會影響到 P1 遍歷原來字符串的內容。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/6980aef0-debe-4b4b-8da5-8b1befbc1408.gif" width="230px"> </div><br>

```java
public String replaceSpace(StringBuffer str) {
    int P1 = str.length() - 1;
    for (int i = 0; i <= P1; i++)
        if (str.charAt(i) == ' ')
            str.append("  ");

    int P2 = str.length() - 1;
    while (P1 >= 0 && P2 > P1) {
        char c = str.charAt(P1--);
        if (c == ' ') {
            str.setCharAt(P2--, '0');
            str.setCharAt(P2--, '2');
            str.setCharAt(P2--, '%');
        } else {
            str.setCharAt(P2--, c);
        }
    }
    return str.toString();
}
```

# 6. 從尾到頭打印鏈表

[NowCoder](https://www.nowcoder.com/practice/d0267f7f55b3412ba93bd35cfa8e8035?tpId=13&tqId=11156&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 題目描述

從尾到頭反過來打印出每個結點的值。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/f5792051-d9b2-4ca4-a234-a4a2de3d5a57.png" width="280px"> </div><br>

## 解題思路

### 使用遞歸

要逆序打印鏈表 1->2->3（3,2,1)，可以先逆序打印鏈表 2->3(3,2)，最後再打印第一個節點 1。而鏈表 2->3 可以看成一個新的鏈表，要逆序打印該鏈表可以繼續使用求解函數，也就是在求解函數中調用自己，這就是遞歸函數。

```java
public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
    ArrayList<Integer> ret = new ArrayList<>();
    if (listNode != null) {
        ret.addAll(printListFromTailToHead(listNode.next));
        ret.add(listNode.val);
    }
    return ret;
}
```

### 使用頭插法

使用頭插法可以得到一個逆序的鏈表。

頭結點和第一個節點的區別：

- 頭結點是在頭插法中使用的一個額外節點，這個節點不存儲值；
- 第一個節點就是鏈表的第一個真正存儲值的節點。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/0dae7e93-cfd1-4bd3-97e8-325b032b716f.gif" width="370px"> </div><br>

```java
public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
    // 頭插法構建逆序鏈表
    ListNode head = new ListNode(-1);
    while (listNode != null) {
        ListNode memo = listNode.next;
        listNode.next = head.next;
        head.next = listNode;
        listNode = memo;
    }
    // 構建 ArrayList
    ArrayList<Integer> ret = new ArrayList<>();
    head = head.next;
    while (head != null) {
        ret.add(head.val);
        head = head.next;
    }
    return ret;
}
```

### 使用棧

棧具有後進先出的特點，在遍歷鏈表時將值按順序放入棧中，最後出棧的順序即為逆序。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/9d1deeba-4ae1-41dc-98f4-47d85b9831bc.gif" width="300px"> </div><br>

```java
public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
    Stack<Integer> stack = new Stack<>();
    while (listNode != null) {
        stack.add(listNode.val);
        listNode = listNode.next;
    }
    ArrayList<Integer> ret = new ArrayList<>();
    while (!stack.isEmpty())
        ret.add(stack.pop());
    return ret;
}
```

# 7. 重建二叉樹

[NowCoder](https://www.nowcoder.com/practice/8a19cbe657394eeaac2f6ea9b0f6fcf6?tpId=13&tqId=11157&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 題目描述

根據二叉樹的前序遍歷和中序遍歷的結果，重建出該二叉樹。假設輸入的前序遍歷和中序遍歷的結果中都不含重複的數字。


<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/31d9adce-2af8-4754-8386-0aabb4e500b0.png" width="300"/> </div><br>

## 解題思路

前序遍歷的第一個值為根節點的值，使用這個值將中序遍歷結果分成兩部分，左部分為樹的左子樹中序遍歷結果，右部分為樹的右子樹中序遍歷的結果。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/c269e362-1128-4212-9cf3-d4c12b363b2f.gif" width="330px"> </div><br>

```java
// 緩存中序遍歷數組每個值對應的索引
private Map<Integer, Integer> indexForInOrders = new HashMap<>();

public TreeNode reConstructBinaryTree(int[] pre, int[] in) {
    for (int i = 0; i < in.length; i++)
        indexForInOrders.put(in[i], i);
    return reConstructBinaryTree(pre, 0, pre.length - 1, 0);
}

private TreeNode reConstructBinaryTree(int[] pre, int preL, int preR, int inL) {
    if (preL > preR)
        return null;
    TreeNode root = new TreeNode(pre[preL]);
    int inIndex = indexForInOrders.get(root.val);
    int leftTreeSize = inIndex - inL;
    root.left = reConstructBinaryTree(pre, preL + 1, preL + leftTreeSize, inL);
    root.right = reConstructBinaryTree(pre, preL + leftTreeSize + 1, preR, inL + leftTreeSize + 1);
    return root;
}
```

# 8. 二叉樹的下一個結點

[NowCoder](https://www.nowcoder.com/practice/9023a0c988684a53960365b889ceaf5e?tpId=13&tqId=11210&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 題目描述

給定一個二叉樹和其中的一個結點，請找出中序遍歷順序的下一個結點並且返回。注意，樹中的結點不僅包含左右子結點，同時包含指向父結點的指針。

```java
public class TreeLinkNode {

    int val;
    TreeLinkNode left = null;
    TreeLinkNode right = null;
    TreeLinkNode next = null;

    TreeLinkNode(int val) {
        this.val = val;
    }
}
```

## 解題思路

① 如果一個節點的右子樹不為空，那麼該節點的下一個節點是右子樹的最左節點；

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/b0611f89-1e5f-4494-a795-3544bf65042a.gif" width="220px"/> </div><br>

② 否則，向上找第一個左鏈接指向的樹包含該節點的祖先節點。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/95080fae-de40-463d-a76e-783a0c677fec.gif" width="200px"/> </div><br>

```java
public TreeLinkNode GetNext(TreeLinkNode pNode) {
    if (pNode.right != null) {
        TreeLinkNode node = pNode.right;
        while (node.left != null)
            node = node.left;
        return node;
    } else {
        while (pNode.next != null) {
            TreeLinkNode parent = pNode.next;
            if (parent.left == pNode)
                return parent;
            pNode = pNode.next;
        }
    }
    return null;
}
```

# 9. 用兩個棧實現隊列

[NowCoder](https://www.nowcoder.com/practice/54275ddae22f475981afa2244dd448c6?tpId=13&tqId=11158&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

## 題目描述

用兩個棧來實現一個隊列，完成隊列的 Push 和 Pop 操作。

## 解題思路

in 棧用來處理入棧（push）操作，out 棧用來處理出棧（pop）操作。一個元素進入 in 棧之後，出棧的順序被反轉。當元素要出棧時，需要先進入 out 棧，此時元素出棧順序再一次被反轉，因此出棧順序就和最開始入棧順序是相同的，先進入的元素先退出，這就是隊列的順序。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/3ea280b5-be7d-471b-ac76-ff020384357c.gif" width="350"/> </div><br>

```java
Stack<Integer> in = new Stack<Integer>();
Stack<Integer> out = new Stack<Integer>();

public void push(int node) {
    in.push(node);
}

public int pop() throws Exception {
    if (out.isEmpty())
        while (!in.isEmpty())
            out.push(in.pop());

    if (out.isEmpty())
        throw new Exception("queue is empty");

    return out.pop();
}
```





# 微信公眾號


更多精彩內容將發佈在微信公眾號 CyC2018 上，你也可以在公眾號後臺和我交流學習和求職相關的問題。另外，公眾號提供了該項目的 PDF 等離線閱讀版本，後臺回覆 "下載" 即可領取。公眾號也提供了一份技術面試複習大綱，不僅系統整理了面試知識點，而且標註了各個知識點的重要程度，從而幫你理清多而雜的面試知識點，後臺回覆 "大綱" 即可領取。我基本是按照這個大綱來進行復習的，對我拿到了 BAT 頭條等 Offer 起到很大的幫助。你們完全可以和我一樣根據大綱上列的知識點來進行復習，就不用看很多不重要的內容，也可以知道哪些內容很重要從而多安排一些複習時間。


<br><div align="center"><img width="320px" src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/other/公眾號海報6.png"></img></div>

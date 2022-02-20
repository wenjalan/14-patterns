# Curated List of Top 75 LeetCode Questions to Save Your Time
https://www.teamblind.com/post/New-Year-Gift---Curated-List-of-Top-75-LeetCode-Questions-to-Save-Your-Time-OaM1orEU

## [Best Time to Buy and Sell Stock (easy)](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)
You are given an array prices where prices[i] is the price of a given stock on the ith day.

You want to maximize your profit by choosing a single day to buy one stock and choosing a different day in the future to sell that stock.

Return the maximum profit you can achieve from this transaction. If you cannot achieve any profit, return 0.

Example 1:
```
Input: prices = [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
Note that buying on day 2 and selling on day 1 is not allowed because you must buy before you sell.
```
Example 2:
```
Input: prices = [7,6,4,3,1]
Output: 0
Explanation: In this case, no transactions are done and the max profit = 0.
```
```java
public int maxProfit(int[] prices) {
    // find i and j such that i < j and prices[j] - prices[i] is maximized 
    int buy = 0;
    int max = 0;
    for (int sell = 0; sell < prices.length; sell++) {
        // calculate profit
        int profit = prices[sell] - prices[buy];

        // if buy is lower than sell, set max
        if (prices[buy] < prices[sell]) {
            max = Math.max(profit, max);
        }

        // if buy is higher than sell, set buy to j
        else {
            buy = sell;
        }
    }
    return max;
}
```

## [Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/)
Given an array of distinct integers nums and a target integer target, return the number of possible combinations that add up to target.

The test cases are generated so that the answer can fit in a 32-bit integer.
```java
public int combinationSum4(int[] nums, int target) {
    // dp array
    // dp[i] is the number of ways we can reach i
    int[] dp = new int[target + 1];

    // we can reach 0 always
    dp[0] = 1;

    // for each target up to target
    for (int i = 0; i <= target; i++) {
        // try subtracting each num
        for (int n : nums) {
            // if we can
            if (i >= n) {
                // add the number of ways we can reach i-n
                // to the number of ways we can reach i
                dp[i] += dp[i - n];
            }
        }
    }

    // return the number of ways we can reach target
    return dp[target];
}
```

## [Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/)
Given an unsorted array of integers nums, return the length of the longest consecutive elements sequence.

You must write an algorithm that runs in O(n) time.
```java
public int longestConsecutive(int[] nums) {
    Set<Integer> set = new HashSet<>();
    for (int n : nums) set.add(n);
    int longest = 0;
    for (int n : set) {
        // only check in one direction
        // this prevents checking subsequences at n if there 
        // exists a sequence at n-1
        if (set.contains(n - 1)) continue;
        
        int count = 1;
        while (set.contains(n + 1)) {
            count++;
            n++;
        }
        longest = Math.max(longest, count);
    }
    return longest;
}
```

## [Non-Overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)
Given an array of intervals intervals where intervals[i] = [starti, endi], return the minimum number of intervals you need to remove to make the rest of the intervals non-overlapping.

Example 1:
```
Input: intervals = [[1,2],[2,3],[3,4],[1,3]]
Output: 1
Explanation: [1,3] can be removed and the rest of the intervals are non-overlapping.
```
Example 2:
```
Input: intervals = [[1,2],[1,2],[1,2]]
Output: 2
Explanation: You need to remove two [1,2] to make the rest of the intervals non-overlapping.
```
Example 3:
```
Input: intervals = [[1,2],[2,3]]
Output: 0
Explanation: You don't need to remove any of the intervals since they're already non-overlapping.
```
```java
public int eraseOverlapIntervals(int[][] intervals) {
    // sort intevals by end time
    Arrays.sort(intervals, (a, b) -> {
        return a[1] - b[1];
    });
    
    // count the number of non-overlapping intervals
    int count = 1;
    int end = intervals[0][1];
    
    // for every remaining interval
    for (int i = 1; i < intervals.length; i++) {
        // if the start is after the end
        if (intervals[i][0] >= end) {
            // increase count
            count++;
            // set new end
            end = intervals[i][1];
        }
    }
    
    // return the number of intervals minus the non overlapping
    return intervals.length - count;
}
```

## [Reorder List](https://leetcode.com/problems/reorder-list/)
You are given the head of a singly linked-list. The list can be represented as:

L0 → L1 → … → Ln - 1 → Ln
Reorder the list to be on the following form:

L0 → Ln → L1 → Ln - 1 → L2 → Ln - 2 → …
You may not modify the values in the list's nodes. Only nodes themselves may be changed.

```java
public void reorderList(ListNode head) {
    // get the middle of the list
    ListNode s = head;
    ListNode f = head.next;
    while (f != null && f.next != null) {
        s = s.next;
        f = f.next.next;
    }
    
    // copy the list from mid onwards in reverse
    ListNode l2 = null;
    ListNode c = s.next;
    while (c != null) {
        // save other temp
        ListNode next = c.next;
        
        // save l2 in temp
        ListNode temp = l2;
        
        // set head of l2 to c
        l2 = c;
        
        // set next of head to temp
        l2.next = temp;
        
        // advance c
        c = next;
    }
    
    // cut head
    s.next = null;
    
    // create a new list from both lists
    ListNode l1 = head;
    
    // print l1 and l2
    print(l1);
    print(l2);
    
    // while both lists have nodes remaining
    ListNode sent = new ListNode(-1);
    while (l1 != null && l2 != null) {
        ListNode nextL1 = l1.next;
        ListNode nextL2 = l2.next;
        
        l1.next = null;
        l2.next = null;
        
        sent.next = l1;
        sent.next.next = l2;
        
        sent = sent.next.next;
        l1 = nextL1;
        l2 = nextL2;
    }
    
    // add the last l1 node if it exists
    if (l1 != null) sent.next = l1;
    
    print(head);
    head = sent.next;
}

public void print(ListNode head) {
    ListNode c = head;
    while (c != null) {
        System.out.print(c.val + " -> ");
        c = c.next;
    }
    System.out.println();
}
```

## [Set Matrix Zeros](https://leetcode.com/problems/set-matrix-zeroes/)
Given an m x n integer matrix matrix, if an element is 0, set its entire row and column to 0's, and return the matrix.

You must do it in place.
```java
// matrix[row][col]
public void setZeroes(int[][] matrix) {
    // whether the first row should be 0s
    boolean firstRow = false;

    // whether the first col should be 0s
    boolean firstCol = false;

    // 1. use the first row and the first col to set whether this row/col should be 0s
    for (int i = 0; i < matrix.length; i++) {
        for (int j = 0; j < matrix[0].length; j++) {
            // if element is 0
            if (matrix[i][j] == 0) {
                // if it was the first row or col, set variables
                if (i == 0) firstRow = true;
                if (j == 0) firstCol = true;

                // set [i, 0] to 0 to record this row should be 0s
                matrix[i][0] = 0;

                // set [0, j] to 0 to record this col should be 0s
                matrix[0][j] = 0;
            }
        }
    }

    // 2. set 0s in matrix
    for (int i = 1; i < matrix.length; i++) {
        for (int j = 1; j < matrix[0].length; j++) {
            // if the element in [i][0] is 0 or [0][j] is 0, set this to 0
            if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                matrix[i][j] = 0;
            }
        }
    }

    // 3. set the first row and col, if necessary
    if (firstRow) {
        for (int j = 0; j < matrix[0].length; j++) {
            matrix[0][j] = 0;
        }
    }
    if (firstCol) {
        for (int i = 0; i < matrix.length; i++) {
            matrix[i][0] = 0;
        }
    }
}
```

## [Longest Palindomic Substring](https://leetcode.com/problems/longest-palindromic-substring/)
Given a string s, return the longest palindromic substring in s.
```java
public String longestPalindrome(String s) {
    int longest = 0;
    int maxLeft = 0;
    int maxRight = 0;
    for (int i = 0; i < s.length(); i++) {
        int midpoint = i;
        // expand about center: even case
        int left = midpoint;
        int right = midpoint + 1;
        while (left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
            if ((right - left) > longest) {
                longest = right - left;
                maxLeft = left;
                maxRight = right;
            }
            left--;
            right++;
        }
        
        // expand about center: odd case
        left = midpoint;
        right = midpoint;
        while (left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
            if ((right - left) > longest) {
                longest = right - left;
                maxLeft = left;
                maxRight = right;
            }
            left--;
            right++;
        }
    }
    return s.substring(maxLeft, maxRight + 1);
}
```

## [Kth Smallest](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)
Given the root of a binary search tree, and an integer k, return the kth smallest value (1-indexed) of all the values of the nodes in the tree.
```java
public int kthSmallest(TreeNode root, int k) {
    // min pq
    PriorityQueue<TreeNode> pq = new PriorityQueue<>((a, b) -> {
        return a.val - b.val;
    });
    
    // traverse the entire tree
    traverse(root, pq);
    
    // return the kth value in the pq
    int val = -1;
    for (int i = 0; i < k; i++) {
        val = pq.poll().val;
    }
    return val;
}

public void traverse(TreeNode root, PriorityQueue<TreeNode> q) {
    if (root == null) return;
    q.add(root);
    traverse(root.left, q);
    traverse(root.right, q);
}
```

## [Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)
Given an integer array nums and an integer k, return the k most frequent elements. You may return the answer in any order.
```java
public int[] topKFrequent(int[] nums, int k) {
    // pq
    // int[] = {val, occurrences}, sorted by most -> least
    PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> {
        return b[1] - a[1]; 
    });
    
    // map of integers
    Map<Integer, Integer> map = new TreeMap<>();
    for (int n : nums) {
        map.put(n, map.getOrDefault(n, 0) + 1);
    }
    
    // for each n in map
    for (int n : map.keySet()) {
        int occ = map.get(n);
        int[] pair = new int[] {n, occ};
        pq.add(pair);
    }
    
    // return top k in pq
    int[] ans = new int[k];
    for (int i = 0; i < k; i++) {
        ans[i] = pq.poll()[0];
    }
    return ans;
}
```

## [Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)
There is an integer array nums sorted in ascending order (with distinct values).

Prior to being passed to your function, nums is possibly rotated at an unknown pivot index k (1 <= k < nums.length) such that the resulting array is [nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]] (0-indexed). For example, [0,1,2,4,5,6,7] might be rotated at pivot index 3 and become [4,5,6,7,0,1,2].

Given the array nums after the possible rotation and an integer target, return the index of target if it is in nums, or -1 if it is not in nums.

You must write an algorithm with O(log n) runtime complexity.
Example 1:
```
Input: nums = [4,5,6,7,0,1,2], target = 0
Output: 4
```
Example 2:
```
Input: nums = [4,5,6,7,0,1,2], target = 3
Output: -1
```
Example 3:
```
Input: nums = [1], target = 0
Output: -1
```
```java
public int search(int[] nums, int target) {
    // modified binary search
    // find index of smallest value
    int lo = 0;
    int hi = nums.length - 1;
    while (lo < hi) {
        int mid = (lo + hi) / 2;
        // if mid is greater than hi, move lo higher
        if (nums[mid] > nums[hi]) {
            lo = mid + 1;
        }
        // if mid is less than hi, move hi lower
        else {
            hi = mid;
        }
    }
    int offset = lo;
    System.out.print("offset:" + offset);
    
    // do a binary search while accounting for offset
    lo = 0;
    hi = nums.length - 1;
    while (lo <= hi) {
        // calculate mid with offset
        int mid = (lo + hi) / 2;
        int midOffset = (mid + offset) % nums.length;
        
        // if found return mid
        if (nums[midOffset] == target) return midOffset;
        
        // if too small increase lo
        if (nums[midOffset] < target) lo = mid + 1;
        
        // if too large decrease hi
        if (nums[midOffset] > target) hi = mid - 1;
    }
    return -1;
}
```

## [Unique Paths](https://leetcode.com/problems/unique-paths/)
There is a robot on an m x n grid. The robot is initially located at the top-left corner (i.e., grid[0][0]). The robot tries to move to the bottom-right corner (i.e., grid[m - 1][n - 1]). The robot can only move either down or right at any point in time.

Given the two integers m and n, return the number of possible unique paths that the robot can take to reach the bottom-right corner.

The test cases are generated so that the answer will be less than or equal to 2 * 109.
```java
public int uniquePaths(int m, int n) {
    // dp[i][j] is how many paths we can take to get to there
    int[][] dp = new int[m][n];
    
    // for each square
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            // the robot can only get to an edge in a single way
            if (i == 0 || j == 0) dp[i][j] = 1;
            
            // otherwise, the robot can get here from the left square or the upper square
            else dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
        }
    }
    
    // return the bottom right square
    return dp[m - 1][n - 1];
}
```

## [Remove Nth Node from End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)
Given the head of a linked list, remove the nth node from the end of the list and return its head.
Example 1:
```
Input: head = [1,2,3,4,5], n = 2
Output: [1,2,3,5]
```
Example 2:
```
Input: head = [1], n = 1
Output: []
```
Example 3:
```
Input: head = [1,2], n = 1
Output: [1]
```
```java
public ListNode removeNthFromEnd(ListNode head, int n) {
    // sentinel
    ListNode sent = new ListNode(-1);
    sent.next = head;

    // p1 is positioned before the node to skip
    ListNode p1 = sent; 

    // p2 is used to find the end of the list
    ListNode p2 = sent;

    // advance p2 to n away from p1
    while (n > 0) {
        p2 = p2.next;
        n--;
    }

    // advance both pointers until p2 reaches end
    while (p2.next != null) {
        p1 = p1.next;
        p2 = p2.next;
    }

    // skip a node
    p1.next = p1.next.next;

    // return sent
    return sent.next;
}
```

## [Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)
Given a binary search tree (BST), find the lowest common ancestor (LCA) of two given nodes in the BST.

According to the definition of LCA on Wikipedia: “The lowest common ancestor is defined between two nodes p and q as the lowest node in T that has both p and q as descendants (where we allow a node to be a descendant of itself).”
Example 1:
```
Input: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8
Output: 6
Explanation: The LCA of nodes 2 and 8 is 6.
```
Example 2:
```
Input: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 4
Output: 2
Explanation: The LCA of nodes 2 and 4 is 2, since a node can be a descendant of itself according to the LCA definition.
```
Example 3:
```
Input: root = [2,1], p = 2, q = 1
Output: 2
```

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */

class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        // if root is larger than both p and q, go left
        if (root.val > p.val && root.val > q.val) {
            return lowestCommonAncestor(root.left, p, q);
        }
        // if root is smaller than both p and q, go right
        if (root.val < p.val && root.val < q.val) {
            return lowestCommonAncestor(root.right, p, q);
        }
        // if this value is between p and q, it is the lowest common ancestor
        return root;
    }
}
```

## [Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/)
Given a string s, return the number of palindromic substrings in it.

A string is a palindrome when it reads the same backward as forward.

A substring is a contiguous sequence of characters within the string.

Example 1:
```
Input: s = "abc"
Output: 3
Explanation: Three palindromic strings: "a", "b", "c".
```
Example 2:
```
Input: s = "aaa"
Output: 6
Explanation: Six palindromic strings: "a", "a", "a", "aa", "aa", "aaa".
```
```java
public int countSubstrings(String s) {
    if (s == null || s.isEmpty()) return 0;
    
    // count
    int count = 0;
    
    // for each starting char
    for (int i = 0; i < s.length(); i++) {
        // count the substrings we can form with odd and even lengths
        count += expand(s, i, i);
        count += expand(s, i, i + 1);
    }
    
    // return count
    return count;
}

private int expand(String str, int s, int e) {
    int count = 0;
    while (s >= 0 && e < str.length() && str.charAt(s) == str.charAt(e)) {
        count++;
        s--;
        e++;
    }
    return count;
}
```

## [Rotate Image](https://leetcode.com/problems/rotate-image/)
You are given an n x n 2D matrix representing an image, rotate the image by 90 degrees (clockwise).

You have to rotate the image in-place, which means you have to modify the input 2D matrix directly. DO NOT allocate another 2D matrix and do the rotation.
```java
public void rotate(int[][] m) {
    // transpose the matrix
    // m[i][j] = m[j][i]
    for (int i = 0; i < m.length; i++) {
        for (int j = i; j < m[0].length; j++) {
            int t = m[i][j];
            m[i][j] = m[j][i];
            m[j][i] = t;
        }
    }
    
    // flip the matrix horizontally
    // m[i][j] = m[i][m.length - 1 - j]
    for (int i = 0; i < m.length; i++) {
        for (int j = 0; j < m[0].length / 2; j++) {
            int t = m[i][j];
            m[i][j] = m[i][m[0].length - 1 - j];
            m[i][m[0].length - 1 - j] = t;
        }
    }
}
```

## [Number of Islands](https://leetcode.com/problems/number-of-islands/)
Given an m x n 2D binary grid grid which represents a map of '1's (land) and '0's (water), return the number of islands.

An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.
```java
// store variables to save some time
int n;
int m;
char[][] g;

public int numIslands(char[][] grid) {
    // store variables
    g = grid;
    n = grid.length;
    if (n == 0) return 0;
    m = grid[0].length;
    
    // a count of islands
    int c = 0;
    
    // for the entire grid
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            // if it's a one
            if (g[i][j] == '1') {
                // nuke the island
                nukeIsland(i, j);
                c++;
            }
        }
    }
    
    // return count
    return c;
}

private void nukeIsland(int i, int j) {
    // if indecies are invalid or is land, return
    if (i < 0 || i >= n || j < 0 || j >= m || g[i][j] != '1') return;
    
    // nuke this part of the island
    g[i][j] = '0';
    
    // nuke the surrounding spaces
    nukeIsland(i + 1, j);
    nukeIsland(i - 1, j);
    nukeIsland(i, j + 1);
    nukeIsland(i, j - 1);
}
```

## [Insert Interval](https://leetcode.com/problems/insert-interval/)
You are given an array of non-overlapping intervals intervals where intervals[i] = [starti, endi] represent the start and the end of the ith interval and intervals is sorted in ascending order by starti. You are also given an interval newInterval = [start, end] that represents the start and end of another interval.

Insert newInterval into intervals such that intervals is still sorted in ascending order by starti and intervals still does not have any overlapping intervals (merge overlapping intervals if necessary).

Return intervals after the insertion.
Example 1:
```
Input: intervals = [[1,3],[6,9]], newInterval = [2,5]
Output: [[1,5],[6,9]]
```
Example 2:
```
Input: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
Output: [[1,2],[3,10],[12,16]]
Explanation: Because the new interval [4,8] overlaps with [3,5],[6,7],[8,10].
```
```java
public int[][] insert(int[][] intervals, int[] newInterval) {        
    // a list of intervals to return
    List<int[]> answer = new LinkedList<>();
    
    // add all non overlapping intervals at start
    int i = 0;
    while (i < intervals.length && intervals[i][1] < newInterval[0]) {
        answer.add(intervals[i]);
        i++;
    }
    
    // merge all intervals that overlap
    while (i < intervals.length && intervals[i][0] <= newInterval[1]) {
        newInterval[0] = Math.min(newInterval[0], intervals[i][0]);
        newInterval[1] = Math.max(newInterval[1], intervals[i][1]);
        i++;
    }
    answer.add(newInterval);
    
    // add all non overlapping intervals at end
    while (i < intervals.length) {
        answer.add(intervals[i]);
        i++;
    }
    
    // return
    return answer.toArray(new int[answer.size()][]);
}
```

## [Contains Duplicate](https://leetcode.com/problems/contains-duplicate/)
Given an integer array nums, return true if any value appears at least twice in the array, and return false if every element is distinct.

```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        // with a Set
        Set<Integer> ints = new HashSet<>();
        for (int n : nums) {
            if (ints.contains(n)) return true;
            ints.add(n);
        }
        return false;
    }
}
```

## [Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)
```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        // calculate the total product of the array
        int totalProduct = 1;
        int zeroCount = 0;
        for (int n : nums) {
            if (n == 0) zeroCount++;
            totalProduct *= n;
        }
        
        // create a new array to store answer
        int[] ans = new int[nums.length];
        
        // if there are two zeros, return ans
        if (zeroCount >= 2) {
            return ans;
        }
        
        // populate ans[i] with the totalProduct / nums[i]
        for (int i = 0; i < ans.length; i++) {
            // if this is the single zero, recalculate
            if (nums[i] == 0) {
                int product = 1;
                for (int j = 0; j < ans.length; j++) {
                    if (j == i) continue;
                    product *= nums[j];
                }
                ans[i] = product;
            }
            else {
                ans[i] = totalProduct / nums[i];
            }
        }
        
        // return
        return ans;
    }
}
```

## [Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/)
```java
class Solution {
    public int maxProduct(int[] nums) {
        int max = nums[0];
        int left = 1;
        int right = 1;
        
        for (int i = 0; i < nums.length; i++) {
            // if left or right turned to 0, reset to 1
            if (left == 0) left = 1;
            if (right == 0) right = 1;
            
            // update running products
            left *= nums[i];
            right *= nums[nums.length - 1 - i];
            
            // update max
            max = Math.max(max, Math.max(left, right));
        }
        
        return max;
    }
}
```

## [Find Minimum in Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)
```java
class Solution {
    public int findMin(int[] nums) {
        int lo = 0;
        int hi = nums.length - 1;
        while (lo < hi) {
            int mid = (lo + hi) / 2;
            // if nums[mid] is greater than nums[hi]
            if (nums[mid] > nums[hi]) {
                // move search upwards
                lo = mid + 1;
            }
            // if nums[mid] is less than nums[hi]
            else {
                // move search downwards
                hi = mid;
            }
        }
        System.out.println("offset: " + lo);
        // return lo
        return nums[lo];
    }
}
```

## [Container with Most Water](https://leetcode.com/problems/container-with-most-water/)
```java
class Solution {
    public int maxArea(int[] height) {
        int left = 0;
        int right = height.length - 1;
        int maxArea = Math.min(height[left], height[right]) * (right - left);
        while (right >= left) {
            int area = Math.min(height[left], height[right]) * (right - left);
            System.out.println("[" + left + ", " + right + "], area: " + area);
            maxArea = Math.max(area, maxArea);
            if (height[left] < height[right]) {
                left++;
            }
            else {
                right--;
            }
        }
        return maxArea;
    }
}
```

## [Merge Intervals](https://leetcode.com/problems/merge-intervals/)
```java
class Solution {
    public int[][] merge(int[][] intervals) {
        // sort intervals
        Arrays.sort(intervals, (int1, int2) -> {
            return int1[0] - int2[0];
        });
        
        // create new intervals
        ArrayList<int[]> newIntervals = new ArrayList<>();
        int[] newInterval = intervals[0];
        for (int i = 1; i < intervals.length; i++) {
            // if this interval begins after the newInterval ends, add the newInterval
            int[] interval = intervals[i];
            if (newInterval[1] < interval[0]) {
                newIntervals.add(newInterval);
                newInterval = interval;
            }
            // otherwise merge this interval with that one
            else {
                newInterval[1] = Math.max(newInterval[1], interval[1]);
            }
        }
        
        // add the last interval
        newIntervals.add(newInterval);
        
        // return the intervals
        return newIntervals.toArray(new int[newIntervals.size()][2]);
    }
}
```
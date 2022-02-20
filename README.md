# The 14 Programming Interview Patterns
## Index
1. [Sliding Window](#1-sliding-window)
2. [Two Pointers](#2-two-pointers)
3. [Fast Pointer, Slow Pointer](#3-fast-and-slow-pointers)
4. [Merge Intervals](#4-merge-intervals)
5. [Cyclic Sort](#5-cyclic-sort) 
6. [Reverse LinkedList](#6-in-place-reversal-of-linkedlist)
7. [Tree BFS](#7-tree-bfs)
8. [Tree DFS](#8-tree-dfs)
9. [Two Heaps](#9-two-heaps)
10. [Subset](#10-subsets)
11. [Modified Binary Search](#11-modified-binary-search)
12. [Top K Elements](#12-top-k-elements)
13. [K-Way Merge](#13-k-way-merge)
14. [Topological Sort](#14-topological-sort)

## 1. Sliding Window
### Overview
Sliding Window problems will ask you to perform an operation on a "window" of an array, list or string. A window is a subset of elements of a data structure, which begins at the first index and progressively slides to the next indecies, and can grow or shrink in size.

### Common Signs
* Input is a linear data structure (array, list, or string)
* Asked to identify longest/shortest substring, subarray, or a desired value
* A brute force solution is of N^2 time

### Code Template
```java
// example code to be modified; currently returns length of largest substring with target unique chars
public int windowTraversal(String str, int target) {
    // use of max or min value will depend on problem
    int max = 0;
    int min = 0;

    // many string-based probems will require a CharMap
    Map<Character, Integer> map = new TreeMap<>();
    for (char c : str) {
        map.put(c, map.getOrDefault(c, 0) + 1);
    }

    // left and right pointers define the boundaries of the window
    int left = 0;
    int right = 0;

    // variables to track progress to target
    int distinct = 0;

    // iterate through the entire string, adjusting left and right pointers
    while (right < str.length()) {
        // if progress variable should increase, expand window right
        if (distinct < target) {
            // perform operations to track progress state here

            // expand window right
            right++;
        }
        // otherwise if progress varaible should decrease, shrink window left
        else {
            // perform operations to track progress state here

            // shrink window left
            left++;
        }

        // check if current state meets target
        if (distinct == target) {
            // update min/max
            min = Math.min(min, right - left);
            max = Math.max(max, right - left);
        }
    }

    // return an answer
    // return max;
    return min;
}
```

### Example Problems
### [Maximum sum subarray of size K](https://leetcode.com/problems/maximum-subarray/) (easy)  

Given an integer array nums, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

A subarray is a contiguous part of an array.

```java
public int maxSubArray(int[] nums) {
    // the initial maximum, depending on problem, can be set to 0 or first element
    int maxSum = nums[0];
    
    // a running sum of a subsequence ending at i
    int sum = 0;
    
    // for every element
    for (int i = 0; i < nums.length; i++) {
        // calculate the new sum
        sum += nums[i];
        
        // if the sum including nums[i] is less than nums[i], reset sum to just nums[i]
        if (sum < nums[i]) {
            sum = nums[i];
        }
        
        // if this sum greater than the max
        if (sum > maxSum) {
            // set the new max
            maxSum = sum;
        }
    }
    
    // return the max sum
    return maxSum;
}
```

### [Longest substring with K distinct characters](https://leetcode.com/problems/longest-substring-with-at-least-k-repeating-characters/) (medium)  

Given a string s and an integer k, return the length of the longest substring of s such that the frequency of each character in this substring is greater than or equal to k.

```java
public int longestSubstring(String s, int k) {
    // the frequencies of all characters in a substring
    int[] freq = new int[26];
    
    // the maximum length of a substring we found
    int maxLen = 0;
    
    // for all possible numbers of distinct chars to include
    // 1 = 1 distinct char, 26 = all chars present in substr
    for (int i = 1; i < 26; i++) {
        // reset the freqs
        Arrays.fill(freq, 0);
        
        // the left and right bounds of the substring
        int left = 0;
        int right = 0;
        
        // the number of distinct chars in the substring
        int distinct = 0;
        
        // the number of chars with K or more instances in
        // the substring
        int kOrMore = 0;
        
        // while the right bound of the substring has room
        while (right < s.length()) {
            // if we need more distinct chars ...
            if (distinct <= i) {
                // ... expand the substring right

                // increase the freq of the rightmost char
                char c = s.charAt(right);
                int index = (int) c - (int) 'a';
                freq[index]++;
                
                // if it's a new char, increment distinct
                if (freq[index] == 1) distinct++;
                
                // if the char appears k or more times,
                // increment kOrMore
                if (freq[index] == k) kOrMore++;
                
                // expand right
                right++;
            }
            // if we need fewer distinct chars...
            // if (i < distinct)
            else {
                // ... contract the substring left

                // decrement the freq of the leftmost char
                char c = s.charAt(left);
                int index = (int) c - (int) 'a';
                freq[index]--;
                
                // if char no longer appears,
                // decrement distinct
                if (freq[index] == 0) distinct--;
                
                // if char no longer appears k times
                // decrement kOrMore
                if (freq[index] == k - 1) kOrMore--;
                
                // shrink left
                left++;
            }
            
            // if we have i distinct chars AND
            // all chars appear kOrMore times,
            // check to see if this is a new max
            if (distinct == i && distinct == kOrMore) {
                // if the length of this new substring is
                // greater than the max, set max
                int len = right - left;
                if (maxLen < len) {
                    maxLen = len;
                }
            }
        }
    }
    
    // return max
    return maxLen;
}
```
### [String anagrams](https://leetcode.com/problems/find-all-anagrams-in-a-string/) (hard)

Given two strings s and p, return an array of all the start indices of p's anagrams in s. You may return the answer in any order.

An Anagram is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

```java
public List<Integer> findAnagrams(String s, String p) {
    // if p is longer than s, no anagrams exist, return empty
    if (p.length() > s.length()) return Collections.emptyList();
    
    // list of start indecies to return
    List<Integer> ans = new LinkedList<>();
    
    // get a mapping of chars in the target string
    Map<Character, Integer> map = new HashMap<>();
    for (char c : p.toCharArray()) {
        map.put(c, map.getOrDefault(c, 0) + 1);
    }
    System.out.println("map: " + map);
    
    // the number of distinct chars in the target
    // string
    int targetCount = map.size();
    
    // left and right boundaries of the substring
    int left = 0;
    int right = 0;
    
    // for the entire string
    while (right < s.length()) {
        // get right char
        char c = s.charAt(right);
        
        // if the map contains right char
        if (map.containsKey(c)) {
            // decrement its count
            map.put(c, map.get(c) - 1);
            
            // if the char no longer exists in map
            // decrement target count
            if (map.get(c) == 0) targetCount--;
        }
        
        // expand right
        right++;
        
        // move left bound while targetCount is 0
        while (targetCount == 0) {
            // get left char
            char leftC = s.charAt(left);
            
            // if map contains left char
            if (map.containsKey(leftC)) {
                // increment its count
                map.put(leftC, map.get(leftC) + 1);
                
                // if leftC occurs more than 0 times, 
                // increment
                if (map.get(leftC) > 0) targetCount++;
            }
            
            // if the substring is the same length as the 
            // target, add the start index to the answer
            if (right - left == p.length()) {
                ans.add(left);
            }
            
            // move left bound
            left++;
        }
    }
    
    // return ans
    return ans;
}
```

## 2. Two Pointers
### Overview
Two Pointers problems will use two pointers to iterate through data structures until a condition is met. Oftentimes this can come in the form of searching pairs in a sorted array or list.

### Common Signs
* Provided data is a sorted array or list, and asked to find a set of elements that fit certain constraints
* Set of elements to be identified is a pair, triplet, or subarray

### Code Template
```java
public List<Integer> twoPointers(int[] sortedArray, int target) {
    // you may have to sort the array
    Arrays.sort(sortedArray);

    // initialize pointers to two ends of array
    int i = 0;
    int j = sortedArray.length - 1;

    // use the sorted property of the array to move pointers appropriately
    while (i < j) {
        if (i + j == target) {
            return Arrays.asList(i, j);
        }
        else if (i + j > target) {
            j--;
        }
        else {
            i++;
        }
    }
}
```

### Example Problems
### [Squaring a sorted array](https://leetcode.com/problems/squares-of-a-sorted-array/) (easy)

Given an integer array nums sorted in non-decreasing order, return an array of the squares of each number sorted in non-decreasing order.

```java
public int[] sortedSquares(int[] nums) {
    // answer array
    int[] ans = new int[nums.length];
    
    // the next index to populate in the answer array
    int i = nums.length - 1;
    
    // left pointer points to start, right points to end
    int left = 0;
    int right = nums.length - 1;
    
    // while the two pointers haven't crossed
    while (left <= right) {
        // get abs value of left and right
        int absLeft = Math.abs(nums[left]);
        int absRight = Math.abs(nums[right]);
        
        // if left is smaller, square left and increment
        if (absLeft > absRight) {
            ans[i] = (int) Math.pow(absLeft, 2);
            left++;
        }
        else {
            ans[i] = (int) Math.pow(absRight, 2);
            right--;
        }
        i--;
    }
    
    // return ans 
    return ans;
}
```

### [Triplets that sum to zero](https://leetcode.com/problems/3sum/) (medium)
Given an integer array nums, return all the triplets [nums[i], nums[j], nums[k]] such that i != j, i != k, and j != k, and nums[i] + nums[j] + nums[k] == 0.

Notice that the solution set must not contain duplicate triplets.

```java
public List<List<Integer>> threeSum(int[] nums) {
    // sort input array
    Arrays.sort(nums);
    
    // answer array
    List<List<Integer>> ans = new LinkedList<>();
    
    // for every possible first element of a triplet
    for (int i = 0; i + 2 < nums.length; i++) {
        // skip duplicate i
        if (i > 0 && nums[i] == nums[i - 1]) continue;
        
        // two additional pointers, one at the next element
        // and one at the end of the array
        int j = i + 1;
        int k = nums.length - 1;
        int target = -nums[i];
        while (j < k) {
            // if the values at these pointers equal 0
            if (nums[i] + nums[j] + nums[k] == 0) {
                // add this set to the answer
                ans.add(Arrays.asList(nums[i], nums[j], nums[k]));
                // move both pointers
                j++;
                k--;
                
                // skip duplicates
                while (j < k && nums[j] == nums[j - 1]) j++;
                while (j < k && nums[k] == nums[k + 1]) k--;
            }
            // if the sum of values at j and k is greater than the difference to i
            else if (nums[j] + nums[k] > target) {
                // decrease k
                k--;
            }
            // otherwise increase j
            else {
                j++;
            }
        }
    }
    
    // return ans
    return ans;
}
```
### [Comparing strings that contain backspaces](https://leetcode.com/problems/backspace-string-compare/) (medium)
Given two strings s and t, return true if they are equal when both are typed into empty text editors. '#' means a backspace character.

Note that after backspacing an empty text, the text will continue empty.
```java
public boolean backspaceCompare(String s, String t) {
    // two pointers for the ends of each string
    int i1 = s.length() - 1;
    int i2 = t.length() - 1;
    
    // counts of backspaces to skip
    int countS = 0;
    int countT = 0;
    
    // while we haven't reached the end of both strings
    while (i1 >= 0 || i2 >= 0) {
        // find next index on S after backspacing
        while (i1 >= 0 && (countS > 0 || s.charAt(i1) == '#')) {
            if (s.charAt(i1) == '#') {
                countS++;
            }
            else countS--;
            i1--;
        }
        
        // get s next char
        char sChar = i1 < 0 ? (char) 0 : s.charAt(i1);
        
        // do the same for t
        while (i2 >= 0 && (countT > 0 || t.charAt(i2) == '#')) {
            if (t.charAt(i2) == '#') countT++;
            else countT--;
            i2--;
        }
        
        // get t next char
        char tChar = i2 < 0 ? (char) 0 : t.charAt(i2);
        
        // if they aren't the same char return false;
        if (sChar != tChar) return false;
        
        // continue
        i1--;
        i2--;
    }
    
    // if we reached the end of both strings, return true
    return true;
}
```

## 3. Fast and Slow Pointers
### Overview
Also referred to as the Hare & Tortoise algorithm. Uses two pointers to traverse an array, sequence or linkedlist at different speeds. Used heavily in dealing with cyclic linkedlists or arrays. The faster pointer will always catch the slower pointer in a loop.

### Common Signs
* Problem mentions a loop in an array or linked list
* Problem mentions needing a position of an element or the length of a linkedlist

### Code Template
```java
public ListNode fastAndSlow(ListNode head) {
    // initialize two pointers
    ListNode slow = head;
    ListNode fast = head;

    // advance the fast pointer at twice the speed as the slow
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }

    // when the fast pointer hits the end, the slow pointer will be at the middle
    ListNode mid = slow;
}
```

Utility code to reverse a LinkedList
```java
// reverses a LinkedList given a head
public ListNode reverse(ListNode head) {
    ListNode prev = null;
    while (head != null) {
        ListNode next = head.next;
        head.next = prev;
        prev = head;
        head = next;
    }
    return prev;
}
```

### Example Problems
### [LinkedList Cycle (easy)](https://leetcode.com/problems/linked-list-cycle/)
Given head, the head of a linked list, determine if the linked list has a cycle in it.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the next pointer. Internally, pos is used to denote the index of the node that tail's next pointer is connected to. Note that pos is not passed as a parameter.

Return true if there is a cycle in the linked list. Otherwise, return false.
```java
public boolean hasCycle(ListNode head) { 
    // initialie two pointers to the head
    ListNode slow = head;
    ListNode fast = head;
    
    // advance slow by 1 each time, and fast by 2 each time
    // condition runs while fast and fast.next both exist
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        
        // if p1 and p2 meet, there is a loop
        if (slow == fast) return true;
    }
    
    // if p1 and p2 both reached the end, there was no loop
    return false;
}
```
### [Palindrome LinkedList (medium)](https://leetcode.com/problems/palindrome-linked-list/)
```java
public boolean isPalindrome(ListNode head) {
    // initialize slow and fast to head
    ListNode fast = head; 
    ListNode slow = head;
    
    // find the middle of the list
    // let fast advance 2 steps and slow advance 1
    // once fast reaches the end, slow will reach the middle
    while (fast != null && fast.next != null) {
        fast = fast.next.next;
        slow = slow.next;
    }
    
    // if fast.next was null, the list has an odd number of nodes
    // advance slow by 1 to make it the larger half of the list
    if (fast != null) {
        slow = slow.next;
    }
    
    // reverse the list headed by slow to get two lists to test for identicality
    ListNode l1 = reverse(slow);
    ListNode l2 = head;
    
    // test for identicality
    while (l1 != null) {
        if (l1.val != l2.val) return false;
        l1 = l1.next;
        l2 = l2.next;
    }
    
    // if all values matched return true
    return true;
}

// reverses a LinkedList given a head
public ListNode reverse(ListNode head) {
    ListNode prev = null;
    while (head != null) {
        ListNode next = head.next;
        head.next = prev;
        prev = head;
        head = next;
    }
    return prev;
}
```
### [Cycle in a Circular Array (hard)](https://leetcode.com/problems/circular-array-loop/)
You are playing a game involving a circular array of non-zero integers nums. Each nums[i] denotes the number of indices forward/backward you must move if you are located at index i:

If nums[i] is positive, move nums[i] steps forward, and
If nums[i] is negative, move nums[i] steps backward.
Since the array is circular, you may assume that moving forward from the last element puts you on the first element, and moving backwards from the first element puts you on the last element.

A cycle in the array consists of a sequence of indices seq of length k where:

Following the movement rules above results in the repeating index sequence seq[0] -> seq[1] -> ... -> seq[k - 1] -> seq[0] -> ...
Every nums[seq[j]] is either all positive or all negative.
k > 1
Return true if there is a cycle in nums, or false otherwise.
```java
public boolean circularArrayLoop(int[] nums) {
    // if length of 1
    if (nums.length == 1) return false;
    
    // for every possible starting position
    int n = nums.length;
    for (int i = 0; i < n; i++) {
        // if i == 0, we already checked this spot
        if (nums[i] == 0) continue;
        
        // slow and fast pointers, fast is initialized to the next move at start
        int slow = i;
        int fast = nextIndex(nums, i);
        
        // while slow and fast aren't visited
        while (nums[fast] * nums[i] > 0 && nums[nextIndex(nums, fast)] * nums[i] > 0) {
            // if slow and fast are the same
            if (slow == fast) {
                // if this was a single element loop
                if (slow == nextIndex(nums, slow)) {
                    // ignore
                    break;
                }
                
                // if not, return true
                return true;
            }
            
            // advance pointers
            slow = nextIndex(nums, slow);
            fast = nextIndex(nums, nextIndex(nums, fast));
        }
        
        // if no loop was found starting at i, set visited elements to 0
        slow = i;
        int move = nums[i];
        while (nums[slow] * move > 0) {
            int next = nextIndex(nums, slow);
            nums[slow] = 0;
            slow = next;
        }
    }
    
    // if no loops were found throughout, return false
    return false;
}

int nextIndex(int[] nums, int i) {
    int n = nums.length;
    return i + nums[i] >= 0? (i + nums[i]) % n: n + ((i + nums[i]) % n);
}
```

## 4. Merge Intervals
### Overview
Problems that deal with overlapping intervals. Usually, asked to find overlapping intervals or to merge intervals if they overlap.

### Common Signs
* Problem asks to produce a list of mutually exclusive intervals
* The term "overlapping intervals" is used

### Code Template
```java
public int[][] merge(int[][] intervals) {
    // if there's one interval return it
    if (intervals.length <= 1) return intervals;
    
    // sort the intervals by their starting positions
    Arrays.sort(intervals, (i1, i2) -> Integer.compare(i1[0], i2[0]));
    
    // a list to store answers
    List<int[]> ans = new LinkedList<>();
    
    // an interval to start with
    int[] newInterval = intervals[0];
    ans.add(newInterval);
    
    // for each interval
    for (int[] interval : intervals) {
        // if these intervals overlap, join them
        if (newInterval[1] >= interval[0]) {
            newInterval[1] = Math.max(newInterval[1], interval[1]);
        }
        // otherwise, add new interval to the list
        else {
            newInterval = interval;
            ans.add(newInterval);
        }
    }
    
    // return answer
    return ans.toArray(new int[ans.size()][]);
}
```

### Example Problems
### [Intervals Intersection (medium)](https://leetcode.com/problems/interval-list-intersections/)
You are given two lists of closed intervals, firstList and secondList, where firstList[i] = [starti, endi] and secondList[j] = [startj, endj]. Each list of intervals is pairwise disjoint and in sorted order.

Return the intersection of these two interval lists.

A closed interval [a, b] (with a <= b) denotes the set of real numbers x with a <= x <= b.

The intersection of two closed intervals is a set of real numbers that are either empty or represented as a closed interval. For example, the intersection of [1, 3] and [2, 4] is [2, 3].
```java
public int[][] intervalIntersection(int[][] a, int[][] b) {
    // if either list is empty return empty
    if (a.length == 0 || b.length == 0) return new int[0][0];
    
    // a list of intervals created by the intersection of a and b
    List<int[]> ans = new LinkedList<>();
    
    // two pointers, start at 0
    int i = 0;
    int j = 0;
    
    // while we haven't reached the end of either list
    while (i < a.length && j < b.length) {
        // find the start with the furthest right value
        int start = Math.max(a[i][0], b[j][0]);
        
        // find the end wit hthe furthest left value
        int end = Math.min(a[i][1], b[j][1]);
        
        // if the start of the interval is before the end
        // an intersection has occurred, add it to the answer
        if (start <= end) ans.add(new int[] {start, end});
        
        // if the end of a is greater than the end of b, move b
        if (a[i][1] > b[j][1]) j++;
        
        // otherwise move a
        else i++;
    }
    
    // return ans
    return ans.toArray(new int[ans.size()][2]);
}
```
### [Task Scheduler (hard)](https://leetcode.com/problems/task-scheduler/)
Given a characters array tasks, representing the tasks a CPU needs to do, where each letter represents a different task. Tasks could be done in any order. Each task is done in one unit of time. For each unit of time, the CPU could complete either one task or just be idle.

However, there is a non-negative integer n that represents the cooldown period between two same tasks (the same letter in the array), that is that there must be at least n units of time between any two same tasks.

Return the least number of units of times that the CPU will take to finish all the given tasks.
```java
// this one's just mathematics
public int leastInterval(char[] tasks, int n) {
    // map of instances of tasks
    // keep track of what the max n instances of tasks was, as well as how many parts have that number of tasks
    Map<Character, Integer> map = new TreeMap<>();
    int max = 0;
    int maxCount = 0;
    for (char c : tasks) {
        map.put(c, map.getOrDefault(c, 0) + 1);
        if (max == map.get(c)) {
            maxCount++;
        }
        else if (max < map.get(c)) {
            max = map.get(c);
            maxCount = 1;
        }
    }
    
    // perform mathematics
    int partCount = max - 1;
    int partLen = n - (maxCount - 1);
    int emptySlots = partCount * partLen;
    int availableTasks = tasks.length - max * maxCount;
    int idles = Math.max(0, emptySlots - availableTasks);
    return tasks.length + idles;
}
```

## 5. Cyclic Sort
### Overview
Cyclic Sort problems will deal with arrays containing numbers of a given range. Iterate over each element, swapping it into its correct place.
### Common Signs
* Input is a sorted array, with numbers in a given range
* Problem asks to find missing, duplicate, or smallest number in a sorted or rotated array
### Code Template
```java
public int cyclicSort(int[] nums) {
    // for all indecies
    for (int i = 0; i < nums.length; i++) {
        // while an element isn't in its proper position
        while (nums[i] != i) {
            // keep swaping it to its right position
            int n = nums[i];
            if (n == nums.length) break;
            int temp = nums[n];
            nums[n] = n;
            nums[i] = temp;
        }
    }

    // return the number still out of place
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] != i) return i;
    }

    // return the last possible index
    return nums.length;
}
```
### Example Problems
### [Find the Missing Number (easy)](https://leetcode.com/problems/missing-number/)
Given an array nums containing n distinct numbers in the range [0, n], return the only number in the range that is missing from the array.
```java
public int missingNumber(int[] nums) {
    // swap each number to its proper spot
    for (int i = 0; i < nums.length; i++) {
        while (nums[i] != i) {
            // swap n to right place
            int n = nums[i];
            // if n is the last number, don't swap it
            if (n == nums.length) break;
            int t = nums[n];
            nums[n] = n;
            nums[i] = t;
        }
    }
    
    // find out which element isn't in the right place
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] != i) {
            return i;
        }
    }
    
    // return the last index if we got here
    return nums.length;
}
```
### [Find the Smallest Missing Positive Number (medium)](https://leetcode.com/problems/first-missing-positive/)
Given an unsorted integer array nums, return the smallest missing positive integer.

You must implement an algorithm that runs in O(n) time and uses constant extra space.
```java
public int firstMissingPositive(int[] nums) {
    // swap all numbers to their right places
    for (int i = 0; i < nums.length; i++) {
        // only perform on positive numbers between 1 and n,
        // while nums aren't in the correct place
        while (nums[i] > 0 && nums[i] <= nums.length && nums[nums[i] - 1] != nums[i]) {
            swap(nums, i, nums[i] - 1);
        }
    }
    
    // search for first missing positive
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] != i + 1) return i + 1;
    }
    
    // if none was found in array, return the length of the array
    return nums.length + 1;
}

private void swap(int[] nums, int i, int j) {
    int t = nums[i];
    nums[i] = nums[j];
    nums[j] = t;
}
```

## 6. In-place Reversal of LinkedList
### Overview
Reverse a LinkedList without use of extra data structures.
### Common Signs
* You are asked to reverse a LinkedList without extra data structures.
### Code Template
```java
public ListNode reverse(ListNode head) {
    ListNode prev = null;
    while (head != null) {
        ListNode next = head.next;
        head.next = prev;
        prev = head;
        head = next;
    }
    return prev;
}
```

### Example Problems
### [Reverse a Sub-list (medium)](https://leetcode.com/problems/reverse-linked-list-ii/)
Given the head of a singly linked list and two integers left and right where left <= right, reverse the nodes of the list from position left to position right, and return the reversed list.
```java
public ListNode reverseBetween(ListNode head, int m, int n) {
    // sentinel points to the head of the list at all times
    ListNode sentinel = new ListNode(-1);
    sentinel.next = head;

    // find the head of the sublist to reverse
    ListNode prev = sentinel;
    ListNode curr = sentinel.next;
    int i = 1;
    for (; i < m; i++) {
        prev = curr;
        curr = curr.next;
    }
    // prev now points to the node before the sublist to reverse
    // curr now points to the first node of the sublist to reverse
    // subhead will point to the node that proceeds the sublist
    ListNode subhead = prev;

    // perform pair-wise swaps until i = n
    for (; i <= n; i++) {
        ListNode temp = curr.next;
        curr.next = prev;
        prev = curr;
        curr = temp;
    }

    // reattach the reversed list to the main list
    subhead.next.next = curr;
    subhead.next = prev;

    // return the head of the original list
    return sentinel.next;
}
```
### [Reverse Every K-Element Sublist](https://leetcode.com/problems/reverse-nodes-in-k-group/)
Given the head of a linked list, reverse the nodes of the list k at a time, and return the modified list.

k is a positive integer and is less than or equal to the length of the linked list. If the number of nodes is not a multiple of k then left-out nodes, in the end, should remain as it is.

You may not alter the values in the list's nodes, only nodes themselves may be changed.
```java
public ListNode reverseKGroup(ListNode head, int k) {
    // ignore empty, singular, or swap 1 list
    if (head == null || head.next == null || k == 1) return head;
    
    // sentinel to keep track of list
    ListNode sentinel = new ListNode(-1);
    sentinel.next = head;
    
    // the start of a sublist
    ListNode start = sentinel;
    int i = 0;
    while (head != null) {
        i++;
        // if this is the start of a sublist
        if (i % k == 0) {
            // reverse the sublist from start to head.next
            start = reverse(start, head.next);
            
            // traverse
            head = start.next;
        } 
        
        // otherwise keep traversing
        else {
            head = head.next;
        }
    }
    
    // return the list
    return sentinel.next;
}

// reverses a LinkedList between two nodes, exclusive
private ListNode reverse(ListNode start, ListNode end) {
    ListNode curr = start.next;
    ListNode prev = start;
    ListNode next = null;
    ListNode first = curr;
    while (curr != end) {
        next = curr.next;
        curr.next = prev;
        prev = curr;
        curr = next;
    }
    start.next = prev;
    first.next = curr;
    return first;
}
```
## 7. Tree BFS
### Overview
Problem will ask you to traverse a tree on a level-by-level basis. Use a Queue. For each node, read the head node, and add its children to the end of the queue.
### Common Signs
* You're asked to traverse a tree level-by-level
### Code Template
### Example Problems
### [Binary Tree Level Order Traversal (easy)](https://leetcode.com/problems/binary-tree-level-order-traversal/)
Given the root of a binary tree, return the level order traversal of its nodes' values. (i.e., from left to right, level by level).
```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> ans = new LinkedList<>();
    Queue<TreeNode> queue = new LinkedList<>();
    traverse(root, ans, queue, 0);
    return ans;
}

private void traverse(TreeNode root, List<List<Integer>> ans, Queue<TreeNode> queue, int level) {
    // if root is null do nothing
    if (root == null) return;
    
    // add all root's children to queue
    if (root.left != null) queue.add(root.left);
    if (root.right != null) queue.add(root.right);
    
    // add this node to the answer in its level's list
    if (ans.size() == level) {
        ans.add(new LinkedList<Integer>());
    }
    ans.get(level).add(root.val);
        
    // add children to answer
    traverse(root.left, ans, queue, level + 1);
    traverse(root.right, ans, queue, level + 1);
}
```
### [Zigzag Traversal (medium)](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)
```java
// same thing as Binary Tree Level Order Traversal, except with an edit to the child order
public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
    List<List<Integer>> ans = new LinkedList<>();
    Queue<TreeNode> queue = new LinkedList<>();
    traverse(root, ans, queue, 0);
    return ans;
}

private void traverse(TreeNode root, List<List<Integer>> ans, Queue<TreeNode> queue, int level) {
    // if root is null do nothing
    if (root == null) return;

    // add all root's children to queue
    if (root.left != null) queue.add(root.left);
    if (root.right != null) queue.add(root.right);

    // add this node to the answer in its level's list
    if (ans.size() == level) {
        ans.add(new LinkedList<Integer>());
    }
    
    // if odd level, insert at beginning
    // if even level, insert at end
    if (level % 2 == 1) {
        ans.get(level).add(0, root.val);
    }
    else {
        ans.get(level).add(root.val);
    }

    // add children to answer
    traverse(root.left, ans, queue, level + 1);
    traverse(root.right, ans, queue, level + 1);
}
```

## 8. Tree DFS
### Overview
Problem will ask you to traverse a tree Pre-Order (parent, left child, right child), In-Order (left child, parent, right child), or Post-Order (left child, right child, parent)
### Common Signs
* Asked to traverse a tree with In-Order, Pre-Order, or Post-Order DFS
* Problem requires searching for something where a node is closer to a leaf
### Code Template
```java
public List<Integer> treeDFS(TreeNode root) {
    // list to store numbers    
    List<Integer> preOrder = treePreOrder(root, new LinkedList<>());
    List<Integer> inOrder = treeInOrder(root, new LinkedList<>());
    List<Integer> postOrder = treePostOrder(root, new LinkedList<>());
    return inOrder;
}

private void treePreOrder(TreeNode root, List<Integer> list) {
    if (root == null) return;
    list.add(root.val);
    treeInOrder(root.left, list);
    treeInOrder(root.right, list);
}

private void treeInOrder(TreeNode root, List<Integer> list) {
    if (root == null) return;
    treeInOrder(root.left, list);
    list.add(root.val);
    treeInOrder(root.right, list);
}

private void treePostOrder(TreeNode root, List<Integer> list) {
    if (root == null) return;
    treeInOrder(root.left, list);
    treeInOrder(root.right, list);
    list.add(root.val);
}
```

### Example Problems
### [Sum of Path Numbers (medium)](https://leetcode.com/problems/sum-root-to-leaf-numbers/)
```java
public int sumNumbers(TreeNode root) {
    return sumNumbers(root, 0);
}

public int sumNumbers(TreeNode root, int sum) {
    // if root is null return 0
    if (root == null) return 0;
    
    // if leaf, return sum * 10 plus value
    if (root.right == null && root.left == null) return sum * 10 + root.val;
    
    // otherwise, return sum of children
    return sumNumbers(root.left, sum * 10 + root.val) + sumNumbers(root.right, sum * 10 + root.val);
}
```
### [All Paths for a Sum (medium)](https://leetcode.com/problems/path-sum-ii/)
```java
public List<List<Integer>> pathSum(TreeNode root, int targetSum) {
    List<List<Integer>> paths = new ArrayList<>();
    List<Integer> currentPath = new ArrayList<>();
    pathSum(root, targetSum, currentPath, paths);
    return paths;
}

void pathSum(TreeNode root, int sum, List<Integer> path, List<List<Integer>> results) {
    // if null, do nothing
    if (root == null) return;
    
    // add the value to this path
    path.add(root.val);
    
    // if this is a leaf, add this path to results if the sum is correct
    if (root.right == null && root.left == null && sum == root.val) {
        // add path to results
        results.add(new LinkedList<>(path)); // copy to not interfere with further paths
    }
    
    // otherwise traverse left and right
    else {
        pathSum(root.left, sum - root.val, path, results);
        pathSum(root.right, sum - root.val, path, results);
    }
    
    // once this subtree is done, remove this val from path
    path.remove(path.size() - 1);
}
```

## 9. Two Heaps
### Overview
Problems that give a set of elements that can be divided into two parts, one of which we want the greatest element and the other the smallest. When this occurs, the median will be calculated from the tops of both heaps.
### Common Signs
* Mentions Priority Queue or Scheduling
* Problem asks to find smallest/largest/median of elements
* Sometimes useful for dealing with binary trees
### Example Problems
### [Find the Median of a Number Stream (medium)](https://leetcode.com/problems/find-median-from-data-stream/)
The median is the middle value in an ordered integer list. If the size of the list is even, there is no middle value and the median is the mean of the two middle values.

For example, for arr = [2,3,4], the median is 3.
For example, for arr = [2,3], the median is (2 + 3) / 2 = 2.5.
Implement the MedianFinder class:

MedianFinder() initializes the MedianFinder object.
void addNum(int num) adds the integer num from the data stream to the data structure.
double findMedian() returns the median of all elements so far. Answers within 10-5 of the actual answer will be accepted.
```java
class MedianFinder {
    
    // two heaps
    // to reverse a PriorityQueue, use Collections.reverseOrder()
    // in the constructor
    Queue<Long> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
    Queue<Long> minHeap = new PriorityQueue<>();
    
    public void addNum(int num) {
        // add it to the max heap
        maxHeap.add((long) num);
        
        // then move it from the max heap to the min heap
        minHeap.add(maxHeap.poll());
        
        // if the max heap is smaller than the min heap
        // add it back to the max heap
        if (maxHeap.size() < minHeap.size()) {
            maxHeap.add(minHeap.poll());
        }
    }
    
    public double findMedian() {
        // if the max heap is larger, return the top value
        // otherwise return the average of both heaps
        return maxHeap.size() > minHeap.size() 
            ? maxHeap.peek() 
            : (maxHeap.peek() + minHeap.peek()) / 2.0;
    }
}
```

## 10. Subsets
### Overview
Problems dealing with permutations or combinations of a given set of elements. The BFS pattern described here can handle these problems.
### Common Signs
* Asked to find combinations or permutations of a set
### Code Template

### Example Problems
### [Subsets Without Duplicates (medium)](https://leetcode.com/problems/subsets-ii/)
Given an integer array nums that may contain duplicates, return all possible subsets (the power set).

The solution set must not contain duplicate subsets. Return the solution in any order.
```java
public List<List<Integer>> subsetsWithDup(int[] nums) {
    // sort list to find duplicates
    Arrays.sort(nums);
    
    // list to store subsets
    List<List<Integer>> subsets = new LinkedList<>();
    
    // recurse
    f(subsets, new LinkedList<>(), nums, 0);
    
    // return
    return subsets;
}

private void f(List<List<Integer>> res, List<Integer> ls, int[] nums, int pos) {
    // add a copy of the current state of the list to the results
    res.add(new LinkedList<>(ls));
    
    // for each remaining element in nums
    for (int i = pos; i < nums.length; i++) {
        // skip over duplicates
        if (i > pos && nums[i] == nums[i - 1]) continue;
        
        // add this element to the list
        ls.add(nums[i]);
        
        // add all subsets with this list
        f(res, ls, nums, i + 1);
        
        // remove the last element from the list
        ls.remove(ls.size() - 1);
    }
}
```
### [String Permutations by Changing Case (medium)](https://leetcode.com/problems/letter-case-permutation/)
Given a string s, you can transform every letter individually to be lowercase or uppercase to create another string.

Return a list of all possible strings we could create. Return the output in any order.
```java
// using a BFS solution
public List<String> letterCasePermutation(String s) {
    // return if s is null
    if (s == null) return Collections.emptyList();
    
    // queue
    Queue<String> queue = new LinkedList<>();
    queue.add(s);
    
    // for the length of the string
    for (int i = 0; i < s.length(); i++) {
        // skip if digit
        if (Character.isDigit(s.charAt(i))) continue;
        
        // for the rest of the queue
        int size = queue.size();
        for (int j = 0; j < size; j++) {
            // get the chars of the next string
            String cur = queue.poll();
            char[] chs = cur.toCharArray();
            
            // put both permutations of this char in upper and lower case
            // back into the queue
            chs[i] = Character.toUpperCase(chs[i]);
            queue.offer(String.valueOf(chs));
            
            chs[i] = Character.toLowerCase(chs[i]);
            queue.offer(String.valueOf(chs));
        }
    }
    
    // return the list of Strings, from the Queue
    return new LinkedList<>(queue);
}
```

## 11. Modified Binary Search
### Overview
Whenever searching is involved in a sorted array, linkedlist, or matrix, a Binary Search is the best option. Variations on this pattern exist.

### Common Signs
* The problem asks for a certain element of a sorted collection

### Code Template
```java
public void binarySearch(int[] elements, int target) {
    // sort the array if not already
    Arrays.sort(elements);

    int start = 0;
    int middle = elements.length / 2;
    int end = elements.length;
    while (elements[middle] != target) {
        // if larger than target, move left
        if (elements[middle] > target) {
            start = middle + 1;
        }

        // if smaller than target, move right
        else {
            end = middle - 1;
        }

        // reset middle and continue
        middle = (end - start) / 2;
    }
    return elements[middle];
}
```
### Example Problems
### [Order Agnostic Binary Search (easy)](https://leetcode.com/problems/binary-search/)
Given an array of integers nums which is sorted in ascending order, and an integer target, write a function to search target in nums. If target exists, then return its index. Otherwise, return -1.

You must write an algorithm with O(log n) runtime complexity.
```java
// note: code is from an old answer for nums in ascending order
// to swap code to descending, check if first element is larger than last element
public int search(int[] nums, int target) {
    return search(nums, target, 0, nums.length);
}

private int search(int[] nums, int target, int start, int end) {
    // return if empty
    if (nums.length == 0) {
        return -1;
    }
    
    // return if singular element isn't target
    if (end - start <= 1 && nums[start] != target) {
        return -1;
    }
    
    // find pivot point between start and end
    int pivot = start + (end - start) / 2;
    System.out.println("start=" + start + ", pivot=" + pivot + ", end=" + end);
    
    // if pivot is target, return pivot
    if (nums[pivot] == target) return pivot;
    
    // if nums[pivot] is greater than target
    if (nums[pivot] > target) {
        // go left
        return search(nums, target, start, pivot - 1);
    }
    // if nums[pivot] is less than target
    else {
        // go right
        return search(nums, target, pivot, end - 1);
    }
}
```

### [Search in a Sorted, Infinite Array (medium)](https://www.geeksforgeeks.org/find-position-element-sorted-array-infinite-numbers/)
Suppose you have a sorted array of infinite numbers, how would you search an element in the array?
```java
// this problem is theoretical, assume you do not have access to array.length
// like a binary search, but every time you're too small, double the number of the "end"
```

## 12. Top K Elements
### Overview
All problems that ask to find the top/smallest/most frequent K elements among a set. Use a heap.
### Common Signs
* Asked to find top/smallest/most frequent K elements of a set
* Asked to sort an array to find an exact element
### Code Template
### Example Problems
### [Top K Elements (easy)]()
Not found.
### [Top K Freqent Elements (medium)](https://leetcode.com/problems/top-k-frequent-elements/)
```java
public int[] topKFrequent(int[] nums, int k) {
    // map all occurrences of nums
    Map<Integer, Integer> freq = new TreeMap<>();
    for (int n : nums) {
        freq.put(n, freq.getOrDefault(n, 0) + 1);
    }
    
    // sort in pq by occurrences
    PriorityQueue<Integer> pq = new PriorityQueue<>((a, b) -> (freq.get(b) - freq.get(a)));
    pq.addAll(freq.keySet());
    
    // get k most common elements
    int[] ans = new int[k];
    for (int i = 0; i < k; i++) {
        ans[i] = pq.poll();
    }
    return ans;
}
```
## 13. K-Way Merge
### Overview
Problems that involve sets of sorted arrays. Use a Heap to traverse multiple arrays in order.
### Common Signs
* Problem has multiple sorted arrays
* Problem asks to merge or find smallest element among them
### Code Template
See Example Problem 1.

### Example Problems
### [Merge k Sorted Lists (hard)](https://leetcode.com/problems/merge-k-sorted-lists/)
```java
public ListNode mergeKLists(ListNode[] lists) {
    // ignore bad
    if (lists == null || lists.length == 0) return null;
    
    // PQ
    PriorityQueue<ListNode> pq = new PriorityQueue<>((a, b) -> {
        return a.val - b.val;
    });
    
    // ans
    ListNode ans = new ListNode(-1);
    ListNode last = ans;
    
    // start pq with heads of all lists
    for (ListNode head : lists) {
        if (head != null) pq.add(head);
    }
    
    // until there are no nodes remaining
    while (!pq.isEmpty()) {
        // attach the min node to the ans list
        last.next = pq.poll();
        last = last.next;
        
        // add the next node of the original list of that node
        // to the queue
        if (last.next != null) pq.add(last.next);
    }
    
    // return ans
    return ans.next;
}
```

### [Find K Pairs With Smallest Sums (medium)](https://leetcode.com/problems/find-k-pairs-with-smallest-sums/)
You are given two integer arrays nums1 and nums2 sorted in ascending order and an integer k.

Define a pair (u, v) which consists of one element from the first array and one element from the second array.

Return the k pairs (u1, v1), (u2, v2), ..., (uk, vk) with the smallest sums.

Couldn't get this one to work because the method signatures were different but the same concepts. (Discussion)[https://leetcode.com/problems/find-k-pairs-with-smallest-sums/discuss/84551/simple-Java-O(KlogK)-solution-with-explanation].
```java
public class Solution {
    public List<int[]> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        PriorityQueue<int[]> que = new PriorityQueue<>((a,b)->a[0]+a[1]-b[0]-b[1]);
        List<int[]> res = new ArrayList<>();
        if(nums1.length==0 || nums2.length==0 || k==0) return res;
        for(int i=0; i<nums1.length && i<k; i++) que.offer(new int[]{nums1[i], nums2[0], 0});
        while(k-- > 0 && !que.isEmpty()){
            int[] cur = que.poll();
            res.add(new int[]{cur[0], cur[1]});
            if(cur[2] == nums2.length-1) continue;
            que.offer(new int[]{cur[0],nums2[cur[2]+1], cur[2]+1});
        }
        return res;
    }
}
```

## 14. Topological Sort
### Overview
Problems where finding the linear odering of elements that have dependencies on each other. Useful in a graph situation.

### Common Signs
* Problem input is a graph with no directed cycles
* Asked to update all objects in a sorted order
* Have a class of objects following a particular order

### Code Template
```
1. Store all nodes in a min PriorityQueue sorted by degree (how many nodes point towards this one)
2. Remove top node, add to a list
3. Decrease degree of all nodes it points to by 1
4. Repeat until all nodes are removed
```

### Example Problems
### [Minimum Height Trees (medium)](https://leetcode.com/problems/minimum-height-trees/)
```java
public List<Integer> findMinHeightTrees(int n, int[][] edges) {
    // if height 1 return 0
    if (n == 1) return Collections.singletonList(0);

    // create adjacency table from edges
    List<Set<Integer>> adj = new ArrayList<>(n);
    for (int i = 0; i < n; ++i) adj.add(new HashSet<>());
    for (int[] edge : edges) {
        adj.get(edge[0]).add(edge[1]);
        adj.get(edge[1]).add(edge[0]);
    }

    // find all leaves in graph
    List<Integer> leaves = new ArrayList<>();
    for (int i = 0; i < n; ++i)
        if (adj.get(i).size() == 1) leaves.add(i);

    // chop off leaves until we're left with the very last nodes
    while (n > 2) {
        // remove leaves.size() nodes from the pool
        n -= leaves.size();

        // next set of leaves
        List<Integer> newLeaves = new ArrayList<>();

        // for each leaf
        for (int i : leaves) {
            // get its neighbors and add to new list
            int j = adj.get(i).iterator().next();
            adj.get(j).remove(i);
            if (adj.get(j).size() == 1) newLeaves.add(j);
        }

        // set new list
        leaves = newLeaves;
    }
    return leaves;
}
```

## [Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/submissions/)
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        ListNode head = new ListNode();
        ListNode p0 = head;
        ListNode p1 = list1;
        ListNode p2 = list2;
        while (p1 != null && p2 != null) {
            if (p1.val <= p2.val) {
                p0.next = p1;
                p1 = p1.next;
            }
            else {
                p0.next = p2;
                p2 = p2.next;
            }
            p0 = p0.next;
        }
        
        while (p1 != null) {
            p0.next = p1;
            p1 = p1.next;
            p0 = p0.next;
        }
        
        while (p2 != null) {
            p0.next = p2;
            p2 = p2.next;
            p0 = p0.next;
        }
        
        return head.next;
    }
}
```

## [Remove nth From End](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
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
}
```
# The 14 Programming Interview Patterns
## Index
1. [Sliding Window](#1-sliding-window)
2. [Two Pointers](#2-two-pointers)
3. Fast Pointer, Slow Pointer
4. Merge Intervals
5. Cyclic Sort 
6. Reverse LinkedList
7. Tree BFS
8. Tree DFS
9. Two Heaps
10. Subset
11. Modified Binary Search
12. Top K Elements
13. K-Way Merge
14. Topological Sort

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

### Example Problems
### (LinkedList Cycle (easy))[]
### (Palindrome LinkedList (medium))[]
### (Cycle in a Circular Array (hard))[]

## 4. Merge Intervals
### Overview
Problems that deal with overlapping intervals. Usually, asked to find overlapping intervals or to merge intervals if they overlap.

### Common Signs
* Problem asks to produce a list of mutually exclusive intervals
* The term "overlapping intervals" is used

### Code Template

### Example Problems
### (Intervals Intersection (medium))[]
### (Maximum CPU Load (hard))[]

## 5.
### Overview
### Common Signs
### Code Template
### Example Problems

## 6.
### Overview
### Common Signs
### Code Template
### Example Problems

## 7.
### Overview
### Common Signs
### Code Template
### Example Problems

## 8.
### Overview
### Common Signs
### Code Template
### Example Problems

## 9.
### Overview
### Common Signs
### Code Template
### Example Problems

## 10.
### Overview
### Common Signs
### Code Template
### Example Problems

## 11.
### Overview
### Common Signs
### Code Template
### Example Problems

## 12.
### Overview
### Common Signs
### Code Template
### Example Problems

## 13.
### Overview
### Common Signs
### Code Template
### Example Problems

## 14.
### Overview
### Common Signs
### Code Template
### Example Problems
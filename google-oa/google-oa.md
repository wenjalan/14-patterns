# Google OA Questions
Problems sourced from (leetcode)[https://leetcode.com/discuss/interview-question/352460/Google-Online-Assessment-Questions].

## [Maximum Time](https://leetcode.com/discuss/interview-question/396769/)
You are given a string in the form of `hh:mm`, where `hh` represents hours and `mm` represents minutes. Some of the letters are blank, represented by `?`s. Fill in the `?`s such that the time represented by the string is maximized.

### Example 1
```
Input: "?4:5?"
Output: "14:59"
```
### Example 2
```
Input: "23:5?"
Output: "23:59"
```
### Example 3
```
Input: "2?:22"
Output: "23:22"
```
### Example 4
```
Input: "0?:??"
Output: "09:59"
```
### Example 5
```
Input: "??:??"
Output: "23:59"
```

## Solution
```java
public static void main(String[] args) {
    String[] tests = new String[] {
        "?4:5?", 
        "23:5?", 
        "2?:22", 
        "0?:??", 
        "??:??",
        "9?:9?",
    };
    for (String test : tests) {
        System.out.println(test + " -> " + getMaximalTime(test));
    }
}

public static String getMaximalTime(String str) {
    // split string into hours and minutes
    char[] hours = str.split(":")[0].toCharArray();
    char[] minutes = str.split(":")[1].toCharArray();
    
    // minutes 
    // if ones is ?, set to 9
    if (minutes[1] == '?') {
        minutes[1] = '9';
    }
    
    // if tens is set to ?, set to 5
    if (minutes[0] == '?') {
        minutes[0] = '5';
    }
    
    // hours
    // if both are ?? set to 23
    if (hours[0] == '?' && hours[1] == '?') {
        hours[0] = '2';
        hours[1] = '3';
    }
    // if the first digit is ?
    else if (hours[0] == '?') {
        // if second digit is < 4, set to 2
        if (Character.getNumericValue(hours[1]) < 4) {
            hours[0] = '2';
        }
        // otherwise set to 1
        else {
            hours[0] = '1';
        }
    }
    // if the second digit is ?
    else {
        // if the first digit is 1 or 0, set to 9
        if (hours[0] == '1' || hours[0] == '0') {
            hours[1] = '9';
        }
        // otherwise set to 4
        else {
            hours[1] = '3';
        }
    }
    return String.valueOf(hours) + ":" + String.valueOf(minutes);
}
```

## [Min Abs Difference of Server Loads](https://leetcode.com/problems/partition-array-into-two-arrays-to-minimize-sum-difference)
An array of `n` integers represents the load it takes to execute a process on a server. You have two servers. If you split the load as evenly as possible, what is the minimum difference betweeen the total loads of both servers?

### Example 1
```
Input: [1, 2, 3, 4, 5]
Output: 1

Explanation: If server 1 gets [1, 2, 4] and server 2 gets [3, 5], the difference between the total loads (7 and 8), will be 1.
```

## Solution
```java
public static void main(String[] args) {
    int[][] inputs = new int[][] {
        {1, 2, 3, 4, 5},
        {6, 7, 8, 9, 10},
    };
    for (int[] input : inputs) {
        System.out.println(Arrays.toString(input) + " -> " + minAbsDifference(input));
    }
}

public static int minAbsDifference(int[] loads) {
    return 0;
}
```

## [Most Booked Hotel Room](https://leetcode.com/discuss/interview-question/421787/)
Given a hotel which has 10 floors [0-9] and each floor has 26 rooms [A-Z]. You are given a sequence of rooms, where + suggests room is booked, - room is freed. You have to find which room is booked maximum number of times.

You may assume that the list describe a correct sequence of bookings in chronological order; that is, only free rooms can be booked and only booked rooms can be freeed. All rooms are initially free. Note that this does not mean that all rooms have to be free at the end. In case, 2 rooms have been booked the same number of times, return the lexographically smaller room.

You may assume:

N (length of input) is an integer within the range [1, 600]
each element of array A is a string consisting of three characters: "+" or "-"; a digit "0"-"9"; and uppercase English letter "A" - "Z"
the sequence is correct. That is every booked room was previously free and every freed room was previously booked.
Example:
```
Input: ["+1A", "+3E", "-1A", "+4F", "+1A", "-3E"]
Output: "1A"
Explanation: 1A as it has been booked 2 times.
```
```java
public static void main(String[] args) {
    String[][] inputs = new String[][] {
        {"+1A", "+3E", "-1A", "+4F", "+1A", "-3E"}
    };
    for (String[] input : inputs) {
        System.out.println(Arrays.toString(input) + " -> " + mostBooked(input));
    }
}

public static String mostBooked(String[] bookings) {
    TreeMap<String, Integer> bookingCount = new TreeMap<>();
    for (String booking : bookings) {
        if (booking.startsWith("+")) {
            String room = booking.substring(1);
            bookingCount.putIfAbsent(room, 0);
            bookingCount.put(room, bookingCount.get(room) + 1);
        }
    }
    
    // get max
    String maxRoom = "";
    int max = 0;
    for (String room : bookingCount.keySet()) {
        int c = bookingCount.get(room);
        if (c > max) {
            maxRoom = room;
            max = c;
        }
    }
    return maxRoom;
}
```

## [Time to Type a String](https://leetcode.com/discuss/interview-question/356477)
Imagine you have a special keyboard with all keys in a single row. The layout of characters on a keyboard is denoted by a string keyboard of length 26. Initially your finger is at index 0. To type a character, you have to move your finger to the index of the desired character. The time taken to move your finger from index i to index j is abs(j - i).

Given a string keyboard that describe the keyboard layout and a string text, return an integer denoting the time taken to type string text.
```
Example 1:

Input: keyboard = "abcdefghijklmnopqrstuvwxy", text = "cba" 
Output: 4
Explanation:
Initially your finger is at index 0. First you have to type 'c'. The time taken to type 'c' will be abs(2 - 0) = 2 because character 'c' is at index 2.
The second character is 'b' and your finger is now at index 2. The time taken to type 'b' will be abs(1 - 2) = 1 because character 'b' is at index 1.
The third character is 'a' and your finger is now at index 1. The time taken to type 'a' will be abs(0 - 1) = 1 because character 'a' is at index 0.
The total time will therefore be 2 + 1 + 1 = 4.
```
Constraints:

length of keyboard will be equal to 26 and all the lowercase letters will occur exactly once;
the length of text is within the range [1..100,000];
string text contains only lowercase letters [a-z].
```java
public static void main(String[] args) {
    List<String[]> inputs = new LinkedList<>();
    inputs.add(new String[] {"abcdefghijklmnopqrstuvwxy", "cba"});
    for (String[] input : inputs) {
        System.out.println(Arrays.toString(input) + " -> " + timeToType(input[0], input[1]));
    }
}

public static int timeToType(String keyboard, String text) {
    // return if empty
    if (text.isEmpty()) return 0;
    
    // map of chars to their index
    Map<Character, Integer> keys = new TreeMap<>();
    for (int i = 0; i < keyboard.length(); i++) {
        char c = keyboard.charAt(i);
        keys.put(c, i);
    }
    
    // running total of time
    // add time to navigate first char from beginning
    int time = keys.get(text.charAt(0));
    
    // the last index we visited
    int lastIndex = time;
    
    // for all text
    for (int i = 1; i < text.length(); i++) {
        char c = text.charAt(i);
        // index of the next char
        int nextIndex = keys.get(c);
        
        // if next index is lower on the keyboard
        if (nextIndex < lastIndex) {
            // add lastIndex - nextIndex to time
            time += lastIndex - nextIndex;
        }
        // if next index is higher on the keyboard
        else {
            time += nextIndex - lastIndex;
        }
        lastIndex = nextIndex;
    }
    
    // return time
    return time;
}
```

## [Maximum Level Sum of Binary Tree](https://leetcode.com/problems/maximum-level-sum-of-a-binary-tree/)
Given the root of a binary tree, the level of its root is 1, the level of its children is 2, and so on.

Return the smallest level x such that the sum of all the values of nodes at level x is maximal.
Example 1:
```
Input: root = [1,7,0,7,-8,null,null]
Output: 2
Explanation: 
Level 1 sum = 1.
Level 2 sum = 7 + 0 = 7.
Level 3 sum = 7 + -8 = -1.
So we return the level with the maximum sum which is level 2.
```
Example 2:
```
Input: root = [989,null,10250,98693,-89388,null,null,null,-32127]
Output: 2
```
```java
public int maxLevelSum(TreeNode root) {
    // queue for tree nodes
    // Queue<TreeNode> queue = new LinkedList<>();
    
    // a list of level nodes
    List<List<TreeNode>> levels = new LinkedList<>();
    
    // traverse
    traverse(root, levels, /* queue, */ 0);
    
    // find the level with the largest sum
    int max = Integer.MIN_VALUE;
    int maxLevel = 0;
    for (int i = 0; i < levels.size(); i++) {
        int levelSum = 0;
        for (TreeNode n : levels.get(i)) {
            levelSum += n.val;
        }
        if (levelSum > max) {
            max = levelSum;
            maxLevel = i;
        }
    }
    
    // return the max
    // adjust for 1-based index
    return maxLevel + 1;
}

public static void traverse(TreeNode root, List<List<TreeNode>> levels, /* Queue<TreeNode> queue, */ int level) {
    // if null do nothing
    if (root == null) return;
    
    // add this node's children to queue
    // if (root.left != null) queue.add(root.left);
    // if (root.right != null) queue.add(root.right);
    
    // add root node to level
    if (levels.size() == level) {
        levels.add(new LinkedList<TreeNode>());
    }
    levels.get(level).add(root);
    
    // traverse to children
    traverse(root.left, levels, /* queue, */ level + 1);
    traverse(root.right, levels, /* queue, */ level + 1);
}
```

## [Min Number of Chairs](https://leetcode.com/discuss/interview-question/356520)
There are n guests who are invited to a party. The k-th guest will attend the party at time S[k] and leave the party at time E[k].

Given an integer array S and an integer array E, both of length n, return an integer denoting the minimum number of chairs you need such that everyone attending the party can sit down.

Example:
```
Input: S = [1, 2, 6, 5, 3], E = [5, 5, 7, 6, 8]
Output: 3
Explanation:
There are five guests attending the party. 
The 1st guest will arrive at time 1. We need one chair at time 1.
The 2nd guest will arrive at time 2. There are now two guests at the party, so we need two chairs at time 2.
The 5th guest will arrive at time 3. There are now three guests at the party, so we need three chairs at time 3.
The 4th guest will arrive at time 5 and, at the same moment, the 1st and 2nd guests will leave the party.
There are now two (the 4th and 5th) guests at the party, so we need two chairs at time 5.
The 3rd guest will arrive at time 6, and the 4th guest will leave the party at the same time.
There are now two (the 3rd and 5th) guests at the party, so we need two chairs at time 6. 
So we need at least 3 chairs.
```
```java
public static void main(String[] args) {
    List<int[][]> inputs = new LinkedList<>();
    inputs.add(new int[][] {
        {1, 2, 6, 5, 3},
        {5, 5, 7, 6, 8}
    });
    for (int[][] input : inputs) {
        System.out.println("S=" + Arrays.toString(input[0]) + ", E=" + Arrays.toString(input[1]) + " -> " + minNumChairs(input[0], input[1]));
    }
}

public static int minNumChairs(int[] arrivals, int[] departures) {
    if (arrivals.length == 0) return 0;
    // create pairs of arrivals and departure and sort by arrival time
    List<int[]> pairs = new LinkedList<>();
    for (int i = 0; i < arrivals.length; i++) {
        int[] pair = new int[] {arrivals[i], departures[i]};
        pairs.add(pair);
    }
    
    // sort pairs by arrival
    Collections.sort(pairs, (a, b) -> {
        return a[0] - b[0];
    });
    
    // for each interval
    int[] times = new int[pairs.get(pairs.size() - 1)[1] + 1];
    for (int i = 0; i < pairs.size(); i++) {
        System.out.println(Arrays.toString(times));
        int[] pair = pairs.get(i);
        for (int j = pair[0]; j < pair[1]; j++) {
            times[j]++;
        }
    }
    
    // find the maximum value in times
    int max = 0;
    for (int n : times) {
        if (n > max) {
            max = n;
        }
    }
    return max;
}
```

## [K Closest Points to Origin](https://leetcode.com/problems/k-closest-points-to-origin/)
Given an array of points where points[i] = [xi, yi] represents a point on the X-Y plane and an integer k, return the k closest points to the origin (0, 0).

The distance between two points on the X-Y plane is the Euclidean distance (i.e., √(x1 - x2)2 + (y1 - y2)2).

You may return the answer in any order. The answer is guaranteed to be unique (except for the order that it is in).

Example 1:
```
Input: points = [[1,3],[-2,2]], k = 1
Output: [[-2,2]]
Explanation:
The distance between (1, 3) and the origin is sqrt(10).
The distance between (-2, 2) and the origin is sqrt(8).
Since sqrt(8) < sqrt(10), (-2, 2) is closer to the origin.
We only want the closest k = 1 points from the origin, so the answer is just [[-2,2]].
```
Example 2:
```
Input: points = [[3,3],[5,-1],[-2,4]], k = 2
Output: [[3,3],[-2,4]]
Explanation: The answer [[-2,4],[3,3]] would also be accepted.
```
```java
public int[][] kClosest(int[][] points, int k) {
    // pq to sort by distance to origin
    PriorityQueue<int[]> closest = new PriorityQueue<>((a, b) -> {
        return distToOriginSq(a) - distToOriginSq(b);
    });
    
    // add all points
    for (int[] point : points) closest.add(point);
    
    // get min k points
    int[][] results = new int[k][2];
    for (int i = 0; i < k; i++) {
        results[i] = closest.poll();
    }
    return results;
}

public int distToOriginSq(int[] point) {
    return (int) Math.pow(point[0], 2) + (int) Math.pow(point[1], 2);
}
```

## [License Key Reformatting](https://leetcode.com/problems/license-key-formatting/) 
You are given a license key represented as a string s that consists of only alphanumeric characters and dashes. The string is separated into n + 1 groups by n dashes. You are also given an integer k.

We want to reformat the string s such that each group contains exactly k characters, except for the first group, which could be shorter than k but still must contain at least one character. Furthermore, there must be a dash inserted between two groups, and you should convert all lowercase letters to uppercase.

Return the reformatted license key.

Example 1:
```
Input: s = "5F3Z-2e-9-w", k = 4
Output: "5F3Z-2E9W"
Explanation: The string s has been split into two parts, each part has 4 characters.
Note that the two extra dashes are not needed and can be removed.
```
Example 2:
```
Input: s = "2-5g-3-J", k = 2
Output: "2-5G-3J"
Explanation: The string s has been split into three parts, each part has 2 characters except the first part as it could be shorter as mentioned above.
```
```java
public String licenseKeyFormatting(String s, int k) {
    // uppercase the string
    s = s.toUpperCase();
    
    // StringBuffer to store new string
    StringBuffer bf = new StringBuffer();
    
    // for each char, from the end
    int currentGroupLen = 0;
    for (int i = s.length() - 1; i >= 0; i--) {
        char c = s.charAt(i);
        
        // skip existing dashes
        if (c == '-') continue;
        
        // if this was the last char in a group, add a dash
        if (currentGroupLen == k) {
            bf.insert(0, '-');
            currentGroupLen = 0;
        }
        
        // add this char to the buffer's start
        bf.insert(0, c);
        currentGroupLen++;
    }
    
    // return new string
    return bf.toString();
}
```

## [Unique Email Addresses](https://leetcode.com/problems/unique-email-addresses/submissions/)
Every valid email consists of a local name and a domain name, separated by the '@' sign. Besides lowercase letters, the email may contain one or more '.' or '+'.

For example, in "alice@leetcode.com", "alice" is the local name, and "leetcode.com" is the domain name.
If you add periods '.' between some characters in the local name part of an email address, mail sent there will be forwarded to the same address without dots in the local name. Note that this rule does not apply to domain names.

For example, "alice.z@leetcode.com" and "alicez@leetcode.com" forward to the same email address.
If you add a plus '+' in the local name, everything after the first plus sign will be ignored. This allows certain emails to be filtered. Note that this rule does not apply to domain names.

For example, "m.y+name@email.com" will be forwarded to "my@email.com".
It is possible to use both of these rules at the same time.

Given an array of strings emails where we send one email to each emails[i], return the number of different addresses that actually receive mails.
```java
public int numUniqueEmails(String[] emails) {
    Set<String> addresses = new HashSet<>();
    for (String email : emails) {
        addresses.add(stripEmail(email));
    }
    return addresses.size();
}

public String stripEmail(String email) {
    StringBuffer stripped = new StringBuffer();
    // set to lowercase?
    email = email.toLowerCase();
    String localName = email.split("@")[0];
    String domainName = email.split("@")[1];
    
    // for every char in the local name
    for (int i = 0; i < localName.length(); i++) {
        char c = localName.charAt(i);
        // if it's a dot skip
        if (c == '.') continue;
        
        // if it's a plus, terminate the buffer and return
        else if (c == '+') break;
        
        // otherwise add the char to the buffer
        else stripped.append(c);
    }
    
    // return the name
    return stripped.toString() + "@" + domainName; 
}
```

## [Fruit Into Baskets](https://leetcode.com/problems/fruit-into-baskets/)
You are visiting a farm that has a single row of fruit trees arranged from left to right. The trees are represented by an integer array fruits where fruits[i] is the type of fruit the ith tree produces.

You want to collect as much fruit as possible. However, the owner has some strict rules that you must follow:

You only have two baskets, and each basket can only hold a single type of fruit. There is no limit on the amount of fruit each basket can hold.
Starting from any tree of your choice, you must pick exactly one fruit from every tree (including the start tree) while moving to the right. The picked fruits must fit in one of your baskets.
Once you reach a tree with fruit that cannot fit in your baskets, you must stop.
Given the integer array fruits, return the maximum number of fruits you can pick.
```java
public int totalFruit(int[] fruits) {
    // sliding window
    int lastFruitSeen = -1;
    int otherFruitSeen = -1;
    int lastFruitCount = 0;
    int currMax = 0;
    int max = 0;
    
    for (int f : fruits) {
        // if fruit is one we have in the basket increase currMax
        if (f == lastFruitSeen || f == otherFruitSeen) currMax++;
        
        // otherwise reset max to the count of fruit we saw last
        else currMax = lastFruitCount + 1;
        
        // if this fruit was the one we saw last increase its count
        if (f == lastFruitSeen) lastFruitCount++;
        
        // if it was different then reset last fruit count
        else lastFruitCount = 1;
        
        // if the fruit was different, set the last fruit to this one
        if (f != lastFruitSeen) {
            otherFruitSeen = lastFruitSeen;
            lastFruitSeen = f;
        }
        
        // set max
        max = Math.max(max, currMax);
    }
    return max;
}
```

## [Min Days to Bloom](https://leetcode.com/discuss/interview-question/334191)
Given an array of roses. roses[i] means rose i will bloom on day roses[i]. Also given an int k, which is the minimum number of adjacent bloom roses required for a bouquet, and an int n, which is the number of bouquets we need. Return the earliest day that we can get n bouquets of roses.

Example:
```
Input: roses = [1, 2, 4, 9, 3, 4, 1], k = 2, n = 2
Output: 4
Explanation:
day 1: [b, n, n, n, n, n, b]
The first and the last rose bloom.

day 2: [b, b, n, n, n, n, b]
The second rose blooms. Here the first two bloom roses make a bouquet.

day 3: [b, b, n, n, b, n, b]

day 4: [b, b, b, n, b, b, b]
Here the last three bloom roses make a bouquet, meeting the required n = 2 bouquets of bloom roses. So return day 4.
```
```java
public static void main(String[] args) {
    int[] roses = new int[] {1, 2, 4, 9, 3, 4, 1};
    int k = 2;
    int n = 2;
    int ans = minDaysToBloom(roses, k, n);
    System.out.println(ans);
}

// binary search over n days
public static int minDaysToBloom(int[] roses, int k, int n) {
    // the maximum days a rose needs to bloom
    int max = roses[0];
    for (int r : roses) max = Math.max(r, max);
    
    int left = 1;
    int right = max;
    int mid = max / 2;
    while (left != right) {
        // update mid
        mid = (left + right) / 2;
        
        // number of bouquets we can make on day mid
        int x = nBouquetsOn(roses, k, mid);
        
        // print state
        // System.out.println("left=" + left + "\nright=" + right + "\nmid=" + mid + "\nx=" + x);
        
        // if we have fewer than we need, increase the days
        if (x < n) {
            left = mid + 1;
        }
        // if we have more than we need, decrease the days
        else {
            right = mid;
        }
    }
    return mid;
}

static int nBouquetsOn(int[] roses, int k, int day) {
    // count bouquets
    int count = 0;
    int curr = 0;
    for (int i = 0; i < roses.length; i++) {
        if (roses[i] - day <= 0) {
            // increase curr
            curr++;
        }
        else {
            // reset curr
            curr = 0;
        }
        
        // if we have enough to make a bouquet
        if (curr == k) {
            // increment count
            count++;
            // System.out.println(count);
            curr = 0;
        }
    }
    return count;
}
```
## [Decreasing Subsequences](https://leetcode.com/playground/D2aYgBVB)
Given an int array nums of length n. Split it into strictly decreasing subsequences. Output the min number of subsequences you can get by splitting.

Example 1:
```
Input: [5, 2, 4, 3, 1, 6]
Output: 3
Explanation:
You can split this array into: [5, 2, 1], [4, 3], [6]. And there are 3 subsequences you get.
Or you can split it into [5, 4, 3], [2, 1], [6]. Also 3 subsequences.
But [5, 4, 3, 2, 1], [6] is not legal because [5, 4, 3, 2, 1] is not a subsuquence of the original array.
```
Example 2:
```
Input: [2, 9, 12, 13, 4, 7, 6, 5, 10]
Output: 4
Explanation: [2], [9, 4], [12, 10], [13, 7, 6, 5]
```
Example 3:
```
Input: [1, 1, 1]
Output: 3
Explanation: Because of the strictly descending order you have to split it into 3 subsequences: [1], [1], [1])
```
```java
public static void main(String[] args) {
    int[][] inputs = new int[][] {
        {5, 2, 4, 3, 1, 6},
        {2, 9, 12, 13, 4, 7, 6, 5, 10},
        {1, 1, 1}
    };
    for (int[] input : inputs) {
        System.out.println(decreasingSubsequence(input));
    }
}

public static int decreasingSubsequence(int[] arr) {
    // list of ss
    List<List<Integer>> subs = new LinkedList<>();
    
    // add first num
    subs.add(new LinkedList<>());
    subs.get(0).add(arr[0]);
    
    // for each remaining
    for (int i = 1; i < arr.length; i++) {
        System.out.println(subs);
        int n = arr[i];
        
        // try adding this number to the earliest created ss
        boolean flag = false;
        for (List<Integer> seq : subs) {
            if (seq.get(seq.size() - 1) > n) {
                seq.add(n);
                flag = true;
                break;
            }
        }
        if (flag) continue;
        
        // otherwise create a new subsequence
        List<Integer> ss = new LinkedList<>();
        ss.add(n);
        subs.add(ss);
    }
    
    // return the size of subs
    return subs.size();
}
```
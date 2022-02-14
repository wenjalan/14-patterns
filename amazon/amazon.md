# Amazon OA 2022
Discussion on [LeetCode](https://leetcode.com/discuss/interview-question/1183360/Amazon-Online-Assesment-Questions)

## Top N Competitors
Given a list of Strings containing the names of competing companies and another list of Strings containing reviews, return the top N most frequently mentioned companies.

### Example
```
competitors = [newshop, shopnow, afashion, fashionbeats, mymarket, tcellular]
numReviews = 6
reviews = [
"newshop is providing good services in the city; everyone should use newshop",
"best services by newshop",
"fashionbeats has great services in the city",
"I am proud to have fashionbeats",
"mymarket has awesome services",
"Thanks Newshop for the quick delivery"]
```

### Output
```
["newshop", "fashionbeats"]
```

### Explanation
`"newshop"` is occurring in 3 different reviews. `"fashionbeats"` is occuring in 2 different user reviews and `"mymarket"` is occurring in only 1 review.

## Solution
```java
public static void main(String[] args) {
    List<String> reviews = new ArrayList(
            Arrays.asList("newshop is providing good services in the city; everyone should use newshop",
                    "best services by newshop", "fashionbeats has great services in the city",
                    "I am proud to have fashionbeats", "mymarket has awesome services",
                    "Thanks Newshop for the quick delivery"));
    List<String> competitors = new ArrayList<String>(
            Arrays.asList("newshop", "shopnow", "afashion", "fashionbeats", "mymarket", "tcellular"));
    System.out.println(topNCompetitors(competitors, reviews, 2));
}

// 1. Count occurrences of companies in reviews
// 2. Sort companies by their appearances in a PQ
// 3. Return the top N companies, by appearance
// RT: O(N log N), worst operation is PQ insertion    
public static List<String> topNCompetitors(List<String> companies, List<String> reviews, int n) {
    // map companies to number of appearances in a review
    TreeMap<String, Integer> appearances = new TreeMap<>();
    for (String review : reviews) {
        for (String company : companies) {
            review = review.toLowerCase();
            company = company.toLowerCase();
            if (review.contains(company)) {
                appearances.putIfAbsent(company, 0);
                appearances.put(company, appearances.get(company) + 1);
            }
        }
    }

    // sort into a PQ: descending by appearances
    PriorityQueue<String> pq = new PriorityQueue<>((a, b) -> appearances.get(b) - appearances.get(a));
    for (String company : appearances.keySet()) {
        pq.add(company);
    }

    // return top n elements off the pq
    List<String> topN = new LinkedList<>();
    for (int i = 0; i < n; i++) {
        topN.add(pq.poll());
    }
    return topN;
}
```

## Largest Item Association
In order to improve customer experience, Amazon has developed a system to provide recommendations to the customer regarding the item they can purchase. Based on historical customer purchase information, an item association can be defined as - If an item A is ordered by a customer, then item B is also likely to be ordered by the same customer (e.g. Book 1 is frequently orderered with Book 2). All items that are linked together by an item association can be considered to be in the same group. An item without any association to any other item can be considered to be in its own item association group of size 1.

Given a list of item association relationships(i.e. group of items likely to be ordered together), write an algorithm that outputs the largest item association group. If two groups have the same number of items then select the group which contains the item that appears first in lexicographic order.

### Example
```
Input: [
    [item1, item2],
    [item3, item4],
    [item4, item5]
]
```
### Output
```
[item3, item4, item5]
```
### Explanation
```
There are two item association groups:
group1: [Item1, Item2]
group2: [Item3,Item4,Item5]
In the available associations, group2 has the largest association. So, the output is [Item3, Item4, Item5].
```

## Solution
```java
// 1. For each item
// 2. If itemA is in a set, add itemB to its set
// 3. If itemB is in a set, add itemA to its set
// 4. Return the largest set made
// RT: O(N), worst operation is indexing all pairs
// "static void main" must be defined in a public class.
public class Main {
    public static void main(String[] args) {
        String[][] items = new String[][] {
            {"item1", "item2"},
            {"item3", "item4"},
            {"item4", "item5"}
        };
        System.out.println(largestItemAssociation(items));
    }
    
    public static List<String> largestItemAssociation(String[][] items) {
        // List of Sets
        List<Set<String>> groups = new LinkedList<>();

        // for each pair
        boolean foundSet = false;
        for (String[] pair : items) {
            // if itemA is in a set, add itemB to the set
            // if itemB is in a set, add itemA to the set
            for (Set<String> group : groups) {
                if (group.contains(pair[0])) {
                    foundSet = true;
                    group.add(pair[1]);
                    break;
                }
                else if (group.contains(pair[1])) {
                    foundSet = true;
                    group.add(pair[0]);
                    break;
                }
            }

            // if we didn't find a set either belonged to, create a new one
            if (!foundSet) {
                Set<String> newSet = new TreeSet<>();
                newSet.add(pair[0]);
                newSet.add(pair[1]);
                groups.add(newSet);
            }
        }

        // return the largest set
        // some funny stuff here if you want this by lexiographical order
        int maxSize = -1;
        Set<String> maxG = null;
        for (Set<String> g : groups) {
            if (g.size() > maxSize) {
                maxSize = g.size();
                maxG = g;
            }
        }
        return new ArrayList<>(maxG);
    } 
}
```

## Fresh Promotion
Amazon is running a promotion in which customers receive prizes for purchasing a secret combination of fruits. The combination will change each day, and the team running the promotion wants to use a code list to make it easy to change the combination. The code list contains groups of fruits. Both the order of the groups within the code list and the order of the fruits within the groups matter. However, between the groups of fruits, any number, and type of fruit is allowable. The term "anything" is used to allow for any type of fruit to appear in that location within the group.

Consider the following secret code list: 

```
[[apple, apple], [banana, anything, banana]]
```

Based on the above secret code list, a customer who made either of the following purchases would win the prize:

```
orange, apple, apple, banana, orange, banana
apple, apple, orange, orange, banana, apple, banana, banana
```
Write an algorithm to output 1 if the customer is a winner else output 0.

### Example 1:
```
Input: codeList = [[apple, apple], [banana, anything, banana]] shoppingCart = [orange, apple, apple, banana, orange, banana]
Output: 1
```
Explanation:
codeList contains two groups - [apple, apple] and [banana, anything, banana].
The second group contains 'anything' so any fruit can be ordered in place of 'anything' in the shoppingCart. The customer is a winner as the customer has added fruits in the order of fruits in the groups and the order of groups in the codeList is also maintained in the shoppingCart.

### Example 2:
```
Input: codeList = [[apple, apple], [banana, anything, banana]]
shoppingCart = [banana, orange, banana, apple, apple]
Output: 0
```
Explanation:
The customer is not a winner as the customer has added the fruits in order of groups but group [banana, orange, banana] is not following the group [apple, apple] in the codeList.

### Example 3:
```
Input: codeList = [[apple, apple], [banana, anything, banana]] shoppingCart = [apple, banana, apple, banana, orange, banana]
Output: 0
```
Explanation:
The customer is not a winner as the customer has added the fruits in an order which is not following the order of fruit names in the first group.

### Example 4:
```
Input: codeList = [[apple, apple], [apple, apple, banana]] shoppingCart = [apple, apple, apple, banana]
Output: 0
```
Explanation:
The customer is not a winner as the first 2 fruits form group 1, all three fruits would form group 2, but can't because it would contain all fruits of group 1.

## Solution
```java
public class Main {
    public static void main(String[] args) {
        String[][] codeList = new String[][] {
            {"a", "a"},
            {"1", "2", "3"}
        };
        String[] cart = new String[] {"1", "2", "3", "4"};
        System.out.println(winner(codeList, cart));
    }
    
    public static int winner(String[][] codeList, String[] cart) {
        // when codeList[i][j] == codeList[m][n], return 1
        int i = 0;
        int j = 0;

        // for each item in shopping cart, in order
        for (String item : cart) {
            // if i and j are at the end, return true
            if (i == codeList.length - 1 && j == codeList[codeList.length - 1].length - 1) return 1;

            // if j is at the end, reset j and increment i
            if (j == codeList[i].length) {
                i++;
                j = 0;
            }

            System.out.println("item: " + item);
            System.out.println("codeListNext: " + codeList[i][j]);

            // if the item at [i][j] != item or item isn't anything, reset pointer to first item in incomplete group
            if (item != codeList[i][j] || !codeList[i][j].equalsIgnoreCase("anything")) {
                System.out.println("reset");
                j = 0;
            }

            // otherwise continue
            j++;
        }

        // return if we've hit the end
        return (i == codeList.length && j == codeList[i - 1].length) ? 1 : 0;
    }
}
```

## Favorite Genres
Given a map `Map<String, List<String>> userSongs` with user names as keys and a list of all the songs that the user has listened to as values.

Also given a map `Map<String, List<String>> songGenres`, with song genre as keys and a list of all the songs within that genre as values. The song can only belong to only one genre.

The task is to return a map `Map<String, List<String>>`, where the key is a user name and the value is a list of the user's favorite genre(s). Favorite genre is the most listened to genre. A user can have more than one favorite genre if he/she has listened to the same number of songs per each of the genres.

## Example 1
```
Input:
userSongs = {  
   "David": ["song1", "song2", "song3", "song4", "song8"],
   "Emma":  ["song5", "song6", "song7"]
},
songGenres = {  
   "Rock":    ["song1", "song3"],
   "Dubstep": ["song7"],
   "Techno":  ["song2", "song4"],
   "Pop":     ["song5", "song6"],
   "Jazz":    ["song8", "song9"]
}

Output: {  
   "David": ["Rock", "Techno"],
   "Emma":  ["Pop"]
}

Explanation:
David has 2 Rock, 2 Techno and 1 Jazz song. So he has 2 favorite genres.
Emma has 2 Pop and 1 Dubstep song. Pop is Emma's favorite genre.
```

## Example 2
```
Input:
userSongs = {  
   "David": ["song1", "song2"],
   "Emma":  ["song3", "song4"]
},
songGenres = {}

Output: {  
   "David": [],
   "Emma":  []
}
```

## Solution
```java
public Map<String, List<String>> getFavoriteGenres(Map<String, List<String>> userSongs, Map<String, List<String>> songGenres) {
    // map of users to genres to counts
    Map<String, Map<String, Integer>> userToGenreCount = new TreeMap<>();

    // for each user
    for (String user : userSongs) {
        // add this user to the map if they don't exist
        userToGenreCount.putIfAbsent(user, new TreeMap<>());

        // for each of their songs
        for (String song : userSongs.get(user)) {
            // find the genres
            List<String> genres = songGenres.get(song);

            // increment the count of each of the genres to this user's list
            for (String genre : genres) {
                userToGenreCount.get(user).put(genre, userToGenreCount.get(user).getOrDefault(genre, 0) + 1);
            }
        }
    }

    // find the most listened to genres of each user
    Map<String, List<String>> mostListenedTo = new TreeMap<>();
    for (String user : userToGenreCount.keySet()) {
        int max = 0;
        List<String> maxGenres = new LinkedList<>();
        for (String genre : userToGenreCount.get(user)) {
            if (userToGenreCount.get(user).get(genre) > max) {
                max = (userToGenreCount.get(user).get(genre);
                maxGenres.clear();
                maxGenres.add(genre);
            }
            else if (userToGenreCount.get(user).get(genre) == max) {
                maxGenres.add(genre);
            }
        }
        mostListenedTo.put(user, maxGenres);
    }
    return mostListenedTo;
}
```

## Treasure Island
You have a map that marks the location of a treasure island. Some of the map area has jagged rocks and dangerous reefs. Other areas are safe to sail in. There are other explorers trying to find the treasure. So you must figure out a shortest route to the treasure island.

Assume the map area is a two dimensional grid, represented by a matrix of characters. You must start from the top-left corner of the map and can move one block up, down, left or right at a time. The treasure island is marked as X in a block of the matrix. X will not be at the top-left corner. Any block with dangerous rocks or reefs will be marked as D. You must not enter dangerous blocks. You cannot leave the map area. Other areas O are safe to sail in. The top-left corner is always safe. Output the minimum number of steps to get to the treasure.

### Example
```
Input:
[['O', 'O', 'O', 'O'],
 ['D', 'O', 'D', 'O'],
 ['O', 'O', 'O', 'O'],
 ['X', 'D', 'D', 'O']]

Output: 5
Explanation: Route is (0, 0), (0, 1), (1, 1), (2, 1), (2, 0), (3, 0) The minimum route takes 5 steps.
```

### Solution
```java
public int treasureIsland(char[][] map) {
    return t(map, 0, 0, 0);
}

private int t(char[][] map, int i, int j, int steps) {
    // if this is an invalid index return 0
    if (i >= map.length || i < 0 || j >= map[0].length || j < 0 || map[i][j] == 'D') return 10000;

    // if we're here stop
    else if (map[i][j] == 'X') return steps;

    // otherwise, try traversing up down left and right
    return Math.min(
        Math.min(
            // up
            t(map, i + 1, j, steps + 1), 
            // right
            t(map, i, j + 1, steps + 1)
        ),
        Math.min(
            // down
            t(map, i - 1, j, steps + 1),
            // left 
            t(map, i, j - 1, steps + 1)
        )
    );
}
```

## Robot Bounded in a Circle
On an infinite plane, a robot initially stands at (0, 0) and faces north. Note that:

The north direction is the positive direction of the y-axis.
The south direction is the negative direction of the y-axis.
The east direction is the positive direction of the x-axis.
The west direction is the negative direction of the x-axis.
The robot can receive one of three instructions:

"G": go straight 1 unit.
"L": turn 90 degrees to the left (i.e., anti-clockwise direction).
"R": turn 90 degrees to the right (i.e., clockwise direction).
The robot performs the instructions given in order, and repeats them forever.

Return true if and only if there exists a circle in the plane such that the robot never leaves the circle.

```java
class Solution {
    public boolean isRobotBounded(String instructions) {
        // for a robot to never leave a space, it must traverse
        // in all directions equally within the given instruction set
        // after repeating at least 4 times
        
        // map of steps taken in a given direction
        // 0-3 indicates all cardinal directions
        Map<Integer, Integer> m = new HashMap<>();
        m.put(0, 0);
        m.put(1, 0);
        m.put(2, 0);
        m.put(3, 0);
        
        // the current direction of the robot
        int dir = 0;
        
        // repeat the simulation 4 times
        for (int i = 1; i <= 4; i++) {
            // for all instructions
            for (char c : instructions.toCharArray()) {
                // if it's G, increment current dir by 1
                if (c == 'G') {
                    m.put(dir, m.get(dir) + 1);
                }
                // if it's left, decrement dir or set to 3
                else if (c == 'L') {
                    dir = dir == 0 ? 3 : dir - 1;
                }
                // if it's right, increment dir or set to 0
                else {
                    dir = dir == 3 ? 0 : dir + 1;
                }
            }
        }
        
        // return whether north and south are equal and west and east are equal
        return m.get(0) == m.get(2) && m.get(1) == m.get(3);
    }
}
```

## Optimial Utilization
Given 2 lists a and b. Each element is a pair of integers where the first integer represents the unique id and the second integer represents a value. Your task is to find an element from a and an element form b such that the sum of their values is less or equal to target and as close to target as possible. Return a list of ids of selected elements. If no pair is possible, return an empty list.

### Example 1:
```
Input:
a = [[1, 2], [2, 4], [3, 6]]
b = [[1, 2]]
target = 7

Output: [[2, 1]]

Explanation:
There are only three combinations [1, 1], [2, 1], and [3, 1], which have a total sum of 4, 6 and 8, respectively.
Since 6 is the largest sum that does not exceed 7, [2, 1] is the optimal pair.
```
### Example 2:
```
Input:
a = [[1, 3], [2, 5], [3, 7], [4, 10]]
b = [[1, 2], [2, 3], [3, 4], [4, 5]]
target = 10

Output: [[2, 4], [3, 2]]

Explanation:
There are two pairs possible. Element with id = 2 from the list `a` has a value 5, and element with id = 4 from the list `b` also has a value 5.
Combined, they add up to 10. Similarily, element with id = 3 from `a` has a value 7, and element with id = 2 from `b` has a value 3.
These also add up to 10. Therefore, the optimal pairs are [2, 4] and [3, 2].
```
### Example 3:
```
Input:
a = [[1, 8], [2, 7], [3, 14]]
b = [[1, 5], [2, 10], [3, 14]]
target = 20

Output: [[3, 1]]
```
### Example 4:
```
Input:
a = [[1, 8], [2, 15], [3, 9]]
b = [[1, 8], [2, 11], [3, 12]]
target = 20

Output: [[1, 3], [3, 2]]
```
## Solution
```java
public List<int[]> optimalUtil(List<int[]> a, List<int[]> b, int target) {
    int bestSum = 0;
    List<int[]> bestIds = new ArrayList<>();
    for (int[] pairA : a) {
        for (int[] pairB : b) {
            int sum = a[1] + b[1];
            if (sum > target) continue;
            else if (sum == bestSum) {
                bestIds.add(new int[] {a[0], b[0]});
            }
            else if (target - sum < target - bestSum) {
                bestSum = sum;
                bestIds.clear();
                bestIds.add(new int[] {a[0], b[0]});
            }
        }
    }
    return bestIds;
}
```

## Partition Labels
You are given a string s. We want to partition the string into as many parts as possible so that each letter appears in at most one part.

Note that the partition is done so that after concatenating all the parts in order, the resultant string should be s.

Return a list of integers representing the size of these parts.

### Example 1:
```
Input: s = "ababcbacadefegdehijhklij"
Output: [9,7,8]
Explanation:
The partition is "ababcbaca", "defegde", "hijhklij".
This is a partition so that each letter appears in at most one part.
A partition like "ababcbacadefegde", "hijhklij" is incorrect, because it splits s into less parts.
```
### Example 2:
```
Input: s = "eccbbbbdec"
Output: [10]
```

### Solution
```java
class Solution {
    public List<Integer> partitionLabels(String s) {
        // create intervals of chars seen
        Map<Character, int[]> charIntervals = new TreeMap<>();
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            // if it's not in the map yet, make a new interval for this char and set start and end to here
            if (!charIntervals.containsKey(c)) {
                int[] interval = new int[2];
                interval[0] = i;
                interval[1] = i;
                charIntervals.put(c, interval);
            }
            // if it already exists, update the end to this index
            else {
                int[] interval = charIntervals.get(c);
                interval[1] = i;
            }
        }

        // // print out our map
        // for (char c : charIntervals.keySet()) {
        //     System.out.println(c + " = " + Arrays.toString(charIntervals.get(c)));
        // }

        // merge the overlapping intervals
        // the intervals are already sorted by start position
        List<int[]> intervals = new ArrayList<>(charIntervals.values());
        intervals.sort((a, b) -> Integer.compare(a[0], b[0]));
        
        List<int[]> answer = new ArrayList<>();
        int[] newInterval = intervals.get(0);
        answer.add(newInterval);

        // for each interval
        for (int[] interval : intervals) {
            // if these intervals overlap, join them
            if (newInterval[1] >= interval[0]) {
                newInterval[1] = Math.max(newInterval[1], interval[1]);
            }
            // otherwise, add newInterval to list
            else {
                newInterval = interval;
                answer.add(newInterval);
            }
        }

        // print out answers
        for (int[] i : answer) {
            System.out.println(s.substring(i[0], i[1]));
        }

        // create an array of the sizes of each interval
        List<Integer> sizes = new ArrayList<>();
        for (int[] i : answer) {
            sizes.add(i[1] - i[0] + 1);
        }
        return sizes;
    }
}
```

## Top K Frequent Words
Given an array of strings words and an integer k, return the k most frequent strings.

Return the answer sorted by the frequency from highest to lowest. Sort the words with the same frequency by their lexicographical order.

### Example 1:
```
Input: words = ["i","love","leetcode","i","love","coding"], k = 2
Output: ["i","love"]
Explanation: "i" and "love" are the two most frequent words.
Note that "i" comes before "love" due to a lower alphabetical order.
```
### Example 2:
```
Input: words = ["the","day","is","sunny","the","the","the","sunny","is","is"], k = 4
Output: ["the","is","sunny","day"]
Explanation: "the", "is", "sunny" and "day" are the four most frequent words, with the number of occurrence being 4, 3, 2 and 1 respectively.
```
### Solution
```java
class Solution {
    public List<String> topKFrequent(String[] words, int k) {
        // map of words to their frequency
        Map<String, Integer> freq = new TreeMap<>();
        for (String word : words) {
            freq.put(word, freq.getOrDefault(word, 0) + 1);
        }
        System.out.println(freq);

        // sort words in pq by freq
        PriorityQueue<String> pq = new PriorityQueue<>((a, b) -> {
            // this funny business to determine ties if freq are equal
            if (freq.get(a) != freq.get(b)) {
                return freq.get(b) - freq.get(a);
            }
            else {
                return a.compareTo(b);
            }
        });
        pq.addAll(freq.keySet());
        System.out.println(pq);

        // return top k elements
        List<String> topK = new ArrayList<>();
        for (int i = 0; i < k; i++) {
            topK.add(pq.poll());
        }
        return topK;
    }
}
```

## Two Sum II
Given a 1-indexed array of integers numbers that is already sorted in non-decreasing order, find two numbers such that they add up to a specific target number. Let these two numbers be numbers[index1] and numbers[index2] where 1 <= index1 < index2 <= numbers.length.

Return the indices of the two numbers, index1 and index2, added by one as an integer array [index1, index2] of length 2.

The tests are generated such that there is exactly one solution. You may not use the same element twice.

Your solution must use only constant extra space.
### Solution
```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0;
        int right = numbers.length - 1;
        while (left < right) {
            if (numbers[left] + numbers[right] == target) {
                return new int[] {left + 1, right + 1};
            }
            else if (numbers[left] + numbers[right] > target) {
                right--;
            }
            else {
                left++;
            }
        }
        return null;
    }
}
```

# Amazon OA 2022
## [Prime Video Award Groups](https://leetcode.com/discuss/interview-question/1735633/Amazon-OA-2022)
Amazon Prime Video is a subscription video-on-demand over-the-top streaming and rental service. The team at Prime Video is developing a method to divide movies into groups based on the number of awards they have won. A group can consist of any number of movies, but the difference in the number of awards won by any two movies in the group must not exceed k.
The movies can be grouped together irrespective of their initial order. Determine the minimum number of groups that can be formed such that each movie is in exactly orly group.
Example
The numbers of awards per movie are awards = [1, 5, 4, 6, 8, 9, 21, and the maximum allowed difference is k = 3.

One way to divide the movies into the minimum number of groups is:

The first group can contain [2, 1], The maximum difference between awards of any two movies is 1 which does not exceed K.
The second group can contain [5, 4, 6], The maximum difference between awards of any two movies is 2 which does not exceed k.
The third group can contain [8,9]. The maximum difference between awards of any two movies is 1 which does not exceed k.
The movies can be divided into a minimum of 3 groups.

Function Description
Complete the function minimumGroups in the
editor below.
minimum Groups has the following parameters:
int awards[n]; the number of awards each movie has earned
int k: the maximum difference in awards counts

## [LRU Cache](https://leetcode.com/problems/lru-cache/)



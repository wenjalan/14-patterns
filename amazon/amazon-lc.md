## [Reorder Logs](https://leetcode.com/problems/reorder-data-in-log-files/submissions/)
```java
class Solution {
    public String[] reorderLogFiles(String[] logs) {
        List<String> digitLogs = new ArrayList<>();
        List<String> letterLogs = new ArrayList<>();
        
        for (String log : logs) {
            if (!isLetterLog(log)) {
                digitLogs.add(log);
            }
            else {
                letterLogs.add(log);
            }
        }
        
        letterLogs.sort((a, b) -> {
            String aIden = a.split(" ")[0];
            String bIden = b.split(" ")[0];
            String aContent = a.substring(aIden.length() + 1);
            String bContent = b.substring(bIden.length() + 1);
            System.out.println("aContent : " + aContent);
            System.out.println("bContent : " + bContent);
            return aContent.equals(bContent) ? aIden.compareTo(bIden) : aContent.compareTo(bContent);
        });
        
        ArrayList<String> reordered = new ArrayList<>();
        for (String log : letterLogs) {
            reordered.add(log);
        }
        for (String log : digitLogs) {
            reordered.add(log);
        }
        return reordered.toArray(new String[reordered.size()]);
    }
    
    private boolean isLetterLog(String log) {
        // return if the last char is not a digit
        String lastChar = log.substring(log.length() - 1);
        // System.out.println(log + " : " + lastChar.matches("[a-z]"));
        return lastChar.matches("[a-z]");
    }
}
```

## [Search Suggestions System](https://leetcode.com/problems/search-suggestions-system/)

## [Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/submissions/)
```java
class Solution {
    public int maxProfit(int[] prices) {
        // buy and sell days
        int buy = 0;
        int sell = 0;
        int max = 0;
        while (sell < prices.length) {
            int prof = prices[sell] - prices[buy];
            if (prof >= 0) {
                max = Math.max(max, prof);
            }
            else {
                buy = sell;
            }
            sell++;
        }
        return max;
    }
}
```

## [Two Sum](https://leetcode.com/problems/two-sum/submissions/)
```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> seen = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int n = nums[i];
            if (seen.containsKey(target - n)) {
                int otherIndex = seen.get(target - n);
                return new int[] {otherIndex, i};
            }
            else {
                seen.put(nums[i], i);
            }
        }
        return null;
    }
}
```

## [Merge Intervals](https://leetcode.com/problems/merge-intervals/submissions/)
```java
class Solution {
    public int[][] merge(int[][] intervals) {
        // sort intervals by start time
        Arrays.sort(intervals, (i1, i2) -> {
            return i1[0] - i2[0];
        });
        
        // merge overlapping
        ArrayList<int[]> newIntervals = new ArrayList<>();
        int[] newInterval = intervals[0];
        for (int i = 1; i < intervals.length; i++) {
            int[] other = intervals[i];
            // if overlap
            if (newInterval[1] >= other[0]) {
                // merge
                newInterval[1] = Math.max(newInterval[1], other[1]);
            }
            // if not, add interval
            else {
                newIntervals.add(newInterval);
                newInterval = other;
            }
        }
        
        // add last interval
        newIntervals.add(newInterval);
        
        // return intervals
        return newIntervals.toArray(new int[newIntervals.size()][2]);
    }
}
```

## [Count Islands](https://leetcode.com/problems/number-of-islands/submissions/)
```java
class Solution {
    public int numIslands(char[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        
        // for each square
        int count = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                // nuke the island at [i, j]
                count += nuke(i, j, m, n, grid);
            }
        }
        
        // return count
        return count;
    }
    
    private int nuke(int i, int j, int m, int n, char[][] grid) {
        // if boundary or 0, return 0
        if (i < 0 || i >= m || j < 0 || j >= n || grid[i][j] == '0') return 0;
        
        // if 1, nuke this part of the island, and its surroundings
        grid[i][j] = '0';
        nuke(i + 1, j, m, n, grid);
        nuke(i - 1, j, m, n, grid);
        nuke(i, j + 1, m, n, grid);
        nuke(i, j - 1, m, n, grid);
        
        // return 1
        return 1;
    }
}
```

## [Meeting Rooms II](https://leetcode.com/problems/meeting-rooms-ii/)
```java
class Solution {
    public int minMeetingRooms(int[][] intervals) {
        // start and end times, sorted
        int[] starts = new int[intervals.length];
        int[] ends = new int[intervals.length];
        for (int i = 0; i < intervals.length; i++) {
            starts[i] = intervals[i][0];
            ends[i] = intervals[i][1];
        }
        Arrays.sort(starts);
        Arrays.sort(ends);
        
        System.out.println("starts: " + Arrays.toString(starts));
        System.out.println("ends: " + Arrays.toString(ends));
        
        // number of current meetings
        int concurrent = 0;
        
        // for each start time
        int j = 0;
        for (int i = 0; i < starts.length; i++) {
            // if this meeting starts before the last one ended
            if (starts[i] < ends[j]) {
                // increase concurrent meetings
                concurrent++;
            }
            // otherwise, find next end time
            else {
                j++;
            }
        }
        
        // return max number of concurrent meetings
        return concurrent;
    }
}
```

## [K Points Closest to Origin](https://leetcode.com/problems/k-closest-points-to-origin/submissions/)
```java
class Solution {
    public int[][] kClosest(int[][] points, int k) {
        PriorityQueue<int[]> ptsByDistance = new PriorityQueue<>((int[] a, int[] b) -> {
            return distToOriginSq(a) - distToOriginSq(b);
        });
        for (int[] pt : points) {
            ptsByDistance.add(pt);
        }
        int[][] ans = new int[k][2];
        for (int i = 0; i < ans.length; i++) {
            ans[i] = ptsByDistance.poll();
        }
        return ans;
    }
    
    int distToOriginSq(int[] pt) {
        int x = pt[0];
        int y = pt[1];
        return ((int) Math.pow(x, 2) + (int) Math.pow(y, 2));
    }
}
```

## [Group Anagrams](https://leetcode.com/problems/group-anagrams/solution/)
```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        // group key to list of words in that key
        HashMap<String, List<String>> groups = new HashMap<>();
        for (String word : strs) {
            String key = anagramKey(word);
            if (groups.containsKey(key)) {
                groups.get(key).add(word);
            }
            else {
                groups.put(key, new ArrayList<>());
                groups.get(key).add(word);
            }
        }
        
        // convert map to List<List>
        List<List<String>> ans = new ArrayList<>();
        for (List<String> group : groups.values()) {
            ans.add(group);
        }
        return ans;
    }
    
    private String anagramKey(String word) {
        // count the letters
        Map<Character, Integer> chars = new HashMap<>();
        for (char c : word.toCharArray()) {
            chars.put(c, chars.getOrDefault(c, 0) + 1);
        }
        
        StringBuffer key = new StringBuffer();
        // for every letter of the alphabet
        for (int i = 97; i < 97 + 26; i++) {
            key.append("|").append(chars.getOrDefault((char) i, 0));
        }
        return key.toString();
    }
}
```
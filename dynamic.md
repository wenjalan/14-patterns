# Dynamic Programming Patterns
See (NeetCode)[https://www.youtube.com/watch?v=mBNrRy2_hVs] video for source.

## 1. Bottom-Up
Problem is solved by using previous answers, starting from a base case and working up.

### Example: Fibonacci Sequence
```java
public static void main(String[] args) {
    System.out.println(fibbonacci(25));
}

// returns the fibbonaci number
// uses bottom-up dynamic programming
public static int fibbonacci(int n) {
    // array to store answers
    int[] dp = new int[n + 1];
    
    // set bases
    dp[0] = 0;
    dp[1] = 1;
    
    // calculate up to n
    for (int i = 2; i <= n; i++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }
    
    // return the nth calculation
    return dp[n];
}
```

## 2. 0/1 Knapsack
Problem is solved by pathfinding on a 2D grid of states. The grid's dimensions include all possible states of decisions and results. Moving through the grid towards a base case results in an answer.

### Example: Can Sum To
```java
public static void main(String[] args) {
    int[] array = new int[] {1, 2, 3};
    int target = 6;
    System.out.println(canSumTo(array, target));
}

public static boolean canSumTo(int[] array, int target) {
    return canSumTo(array, target, 0);
}

public static boolean canSumTo(int[] nums, int target, int index) {
    if (target == 0) return true;
    if (index >= nums.length) return false;
    // return include or skip nums[index]
    return canSumTo(nums, target - nums[index], index + 1) || canSumTo(nums, target, index + 1);
}
```

### Example: [Target Sum](https://leetcode.com/problems/target-sum/)
You are given an integer array nums and an integer target.

You want to build an expression out of nums by adding one of the symbols '+' and '-' before each integer in nums and then concatenate all the integers.

For example, if nums = [2, 1], you can add a '+' before 2 and a '-' before 1 and concatenate them to build the expression "+2-1".
Return the number of different expressions that you can build, which evaluates to target.
```java
public int findTargetSumWays(int[] nums, int target) {
    return findTargetSumWays(nums, target, 0);
}

public int findTargetSumWays(int[] nums, int target, int i) {
    // if i >= nums.length, return 1 if target == 0 and 0 otherwise
    if (i >= nums.length) return target == 0 ? 1 : 0;
    
    // otherwise return the sum of add nums[i] or subtract nums[i]
    return findTargetSumWays(nums, target + nums[i], i + 1) +
        findTargetSumWays(nums, target - nums[i], i + 1);
}
```
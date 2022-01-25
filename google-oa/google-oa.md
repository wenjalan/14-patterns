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

## [Min Abs Difference of Server Loads](https://leetcode.com/discuss/interview-question/356433/)
An array of `n` integers represents the load it takes to execute a process on a server. You have two servers. If you split the load as evenly as possible, what is the minimum difference betweeen the total loads of both servers?

### Example 1
```
Input: [1, 2, 3, 4, 5]
Output: 1

Explanation: If server 1 gets [1, 2, 4] and server 2 gets [3, 5], the difference between the total loads (7 and 8), will be 1.
```
# [January 17, 2022](https://leetcode.com/discuss/interview-experience/1568809/Amazon-or-SDE-Intern-Summer-2022-or-Seattle-(Accepted))
## Behaviorial
1. Tell me about a time when you went over and beyond for a client in your project? (Customer Obsession)
2. Tell me about a time when you took calculated risk in your project? (Bias for Action)

## Technical
1. [Merge Intervals](https://leetcode.com/problems/merge-intervals/)
2. "Then he asked me little bit of data structure questions about tree traversal, tree searching, and hashmap."

# [January 13, 2022](https://leetcode.com/discuss/interview-experience/1687767/Amazon-or-SDE-Intern-or-Seattle-or-Nov-2021)
## Behavioral
1. Tell me time when you suggested some change in the team or project that you worked on.
2. Tell me time when you took a decision without much research or input.

## Technical
1. [Reverse Words in a String](https://leetcode.com/problems/reverse-words-in-a-string/)
```java
class Solution {
    public String reverseWords(String s) {
        String ret = "";
        String buffer = "";
        for (char c : s.toCharArray()) {
            // if c is space, add buffer
            if (c == ' ') {
                // only add if buffer is not empty
                if (buffer.length() > 0) {
                    ret = buffer + " " + ret;
                    buffer = "";
                }
            }
            else {
                // add c to buffer
                buffer += c;
            }
        }
        // add last of buffer
        ret = buffer + " " + ret;
        return ret.trim();
    }
}
```
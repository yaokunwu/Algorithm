# Silding Window

## Pattern
```Java
/* 滑动窗口算法框架 */
class Solution {
    void slidingWindow(string s, string t) {
        HashMap<Character, Integer> need, window;
        for (char c : t) {
            need.put(c, need.getOrDefault(c, 0) + 1);
        }
        int start = 0;
        for (int end = 0; end < s.size(); end++) {
            // c是将移入窗口的字符
            char c = s.charAt(end);
            // 右移窗口
            right++;
            //进行窗口内数据的一系列更新
            
            // debug输出位置 
            System.out.print();
            
            //判断左侧窗口是否要收缩
            while (window needs shrink) {
                // d 是将要移除窗口的字符
                char d = s.charAt(start);
                start++;
            }
        }
    }
}
```
需要变化的地方
* 1. 右指针右移之后窗口数据更新
* 2. 判断窗口是否要收缩
* 3. 左指针右移之后窗口数据更新
* 4. 根据题意计算结果

## Examples
[Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/) <br>
思路：先把要找的T中字符加入哈希表， 然后每遇到一次对应字符就把次数减一，直到3个字符的次数都小于等0。因为要找最小窗口，所以需要对left进行收缩。(做的不是特别熟练)
```Java
class Solution {
    public String minWindow(String s, String t) {
        Map<Character, Integer> countMap = new HashMap<>();
        for (char chr : t.toCharArray()) {
            countMap.put(chr, countMap.getOrDefault(chr, 0) + 1);
        }
        int match = 0;
        String res = "";
        int minLen = Integer.MAX_VALUE;
        int start = 0;
        int head = 0;
        for (int end = 0; end < s.length(); end++) {
            char curr = s.charAt(end);
            if (countMap.containsKey(curr)) {
                countMap.put(curr, countMap.get(curr) - 1);
                if (countMap.get(curr) == 0) {
                    match++;
                }
            }
            while (match == countMap.size()) {
                if (end - start + 1 < minLen) {
                    head = start;
                    minLen = end - start + 1;
                }
                char leftChar = s.charAt(start);
                if (countMap.containsKey(leftChar)) {
                以后一定注意，先查看是否为0
                //这里逻辑也错了。。， 先检查是否为0 再操作match，match是否需要减少只与当前状态的值有关
                    countMap.put(leftChar, countMap.get(leftChar) + 1);
                    if (countMap.get(leftChar) > 0) {
                        match--;
                    }
                }
                start++; 
            }
        }
        return minLen == Integer.MAX_VALUE? "" : s.substring(head, head + minLen);
    }
}
```

[Permutation in String](https://leetcode.com/problems/permutation-in-string/) <br>
思路: sildingwindow, windowsize 不变，每次只需要记录match的数目，发现match就返回
```Java
class Solution {
    public boolean checkInclusion(String s1, String s2) {
        Map<Character, Integer> countMap = new HashMap<>();
        for (char chr : s1.toCharArray()) {
            countMap.put(chr, countMap.getOrDefault(chr, 0) + 1);
        }
        int match = 0, start = 0;
        
        for (int end = 0; end < s2.length(); end++) {
            char currChar = s2.charAt(end);
            if (countMap.containsKey(currChar)) {
                countMap.put(currChar, countMap.get(currChar) - 1);
                if (countMap.get(currChar) == 0) {
                    match++;
                }
            }
            
            if (end >= s1.length() - 1) {
                if (match == countMap.size()) {
                    return true;
                }
                char leftChar = s2.charAt(start);
                // 这里逻辑错了， 因为大于0，并不需要把match的数量减小，因为当这个char数量等于零时，match才会 + 1;
                //if (countMap.containsKey(leftChar)) {
                    //countMap.put(leftChar, countMap.get(leftChar) + 1);
                    //if (countMap.get(leftChar) > 0) {
                       // match--;
                   // }
              // }
                start++;
            }

        }
        return false;
    }
}
```
**注意，这题在写是否match的逻辑错了。。
正确的：
```Java
class Solution {
    public boolean checkInclusion(String s1, String s2) {
        Map<Character, Integer> countMap = new HashMap<>();
        for (char chr : s1.toCharArray()) {
            countMap.put(chr, countMap.getOrDefault(chr, 0) + 1);
        }
        int match = 0, start = 0;
        
        for (int end = 0; end < s2.length(); end++) {
            char currChar = s2.charAt(end);
            if (countMap.containsKey(currChar)) {
                countMap.put(currChar, countMap.get(currChar) - 1);
                if (countMap.get(currChar) == 0) {
                    match++;
                }
            }
            
            if (match == countMap.size()) {
                    return true;
            }
            
            if (end >= s1.length() - 1) {
                char leftChar = s2.charAt(start);
                if (countMap.containsKey(leftChar)) {
                    if (countMap.get(leftChar) == 0) {
                        match--;
                    }
                    countMap.put(leftChar, countMap.get(leftChar) + 1);
                }
                start++;
            }

        }
        return false;
    }
}
```

[Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/)<br>
思路：就是返回s1在s2中所有permutation的起始索引 (没什么问题)
```Java
class Solution {
    public List<Integer> findAnagrams(String s, String p) {
        Map<Character, Integer> countMap = new HashMap<>();
        List<Integer> res = new ArrayList<>();
        for (char chr : p.toCharArray()) {
            countMap.put(chr, countMap.getOrDefault(chr, 0) + 1);
        }
        int start = 0, matched = 0;
        for (int end = 0; end < s.length(); end++) {
            char rightChar = s.charAt(end);
            if (countMap.containsKey(rightChar)) {
                countMap.put(rightChar, countMap.get(rightChar) - 1);
                if (countMap.get(rightChar) == 0) {
                    matched++;
                }
            }
            
            if (matched == countMap.size()) {
                res.add(start);
            }
            
            if (end >= p.length() - 1) {
                char leftChar = s.charAt(start);
                if (countMap.containsKey(leftChar)) {
                    if (countMap.get(leftChar) == 0) {
                        matched--;
                    }
                    countMap.put(leftChar, countMap.get(leftChar) + 1);
                }
                start++;
            }
        }
        return res;
    }
}
```
[Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/) <br>
思路：扩展+收缩. 都不同就判断
```Java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        Map<Character, Integer> idxMap = new HashMap<>();
        int start = 0, maxLen = 0;
        for (int end = 0; end < s.length(); end++) {
            char rightChar = s.charAt(end);
            //如果有重复character， 更新len之前要先重置starting point
            if (idxMap.containsKey(rightChar)) {
                start = Math.max(idxMap.get(rightChar) + 1, start);
            }
            idxMap.put(rightChar, end);
            maxLen = Math.max(maxLen, end - start + 1);
        }
        return maxLen;
    }
}
```

## 总结
* 和双指针题目类似，更像双指针的升级版，滑动窗口的核心点时维护一个窗口集，根据窗口集来进行处理
* 核心步骤
    * right 右移
    * 更新集合
    * left左移
    * 更新集合
    * 求结果

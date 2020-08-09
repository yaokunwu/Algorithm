# Backtracking
## Background
回溯法(backtracking) 常用于遍历列表所有子集，是DFS深度搜索的一种，一般用于全排列，穷尽所有可能，遍历的过程实际上是一个决策树的遍历过程。 时间复杂度一般(O(N!),它不像动态规划存在重叠子问题可以优化，回溯算法就是暴力穷举，复杂度一般都很高. <br>
## Pattern
```Java
int[] result = new int[size];
void backtracking(选择列表，路径）{
    if 满足结束条件 {
        result.add(路径);
        return;
    }
    for (选择 : 选择列表 {
        做选择
        backtracking(选择列表，路径）
        撤销选择
    }
}
# Examples
[Subsets](https://leetcode.com/problems/subsets/) <br>
思路1：递归，参考画出的递归树。 原问题为把n个数的排列组合加进去，子问题为把1个数的排列组合加进去。参数： output数组， subset数组，level指示idx数组，原始选择列表
```Java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        recur(nums, res, 0, new ArrayList<>());
        return res;
    }
    
    private void recur(int[] nums, List<List<Integer>> res, int level, List<Integer> subset) {
        if (level == nums.length) {
            res.add(new ArrayList<>(subset));
            return;
        }
        
        // 进入下一层
        
        选择
        subset.add(nums[level]);
        recur(nums, res, level + 1, subset);
        
        不选择
        subset.remove(subset.size() - 1);
        recur(nums, res, level + 1, subset);
    }
}
```


回溯。。 选择列表[1,2,3] 路径初始化然后做选择，还是不太会。。
```
class Solution {
    List<List<Integer>> res;
    int size;
    public List<List<Integer>> subsets(int[] nums) {
        res = new ArrayList<>();
        for (size = 0; size <= nums.length; size++) {
            backtracking(nums, 0, new ArrayList<>());
        }
        return res;
    }
    
    private void backtracking(int[] nums, int start, List<Integer> sub) {
        if (sub.size() == size) {
            res.add(new ArrayList<>(sub));
            return;
        }
        for (int i = start; i < nums.length; i++) {
            sub.add(nums[i]);
            backtracking(nums, i + 1, sub);
            sub.remove(sub.size() - 1);
        }
    }
}
```
[Subsets II](https://leetcode.com/problems/subsets-ii/) <br>
思路： 需要考虑backtracking时的同层剪枝问题。（需要递归树辅助理解）
```Java
class Solution {
    List<List<Integer>> res;
    int size;
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);
        res = new ArrayList<>();
        for (size = 0; size <= nums.length; size++) {
            backtracking(0, nums, new ArrayList<>());
        }
        return res;
    }
    private void backtracking(int first, int[] nums, List<Integer> subs) {
        if (subs.size() == size) {
            res.add(new ArrayList<>(subs));
            return;
        }
        
        for (int i = first; i < nums.length; i++) {
            if (i > first && nums[i] == nums[i - 1]) {  // 这里是i > first, 才是同层剪枝
                continue;
            }
            subs.add(nums[i]);
            backtracking(i + 1, nums, subs);
            subs.remove(subs.size() - 1);
        }
    }
}
```

[Permutations](https://leetcode.com/problems/permutations/) <br>
思路： 回溯，冲突的发生就是index相同。有冲突就剪枝就完事了。
```java
class Solution {
    List<List<Integer>> res;
    Set<Integer> set;
    public List<List<Integer>> permute(int[] nums) {
        res = new ArrayList<>();
        set = new HashSet<>();
        backtracking(nums, new ArrayList<>());
        return res;
    }
    
    private void backtracking(int[] nums, List<Integer> solution) {
        if (solution.size() == nums.length) {
            res.add(new ArrayList<>(solution));
            return;
        }
        
        for (int i = 0; i < nums.length; i++) {
            if (!set.contains(i)) {
                solution.add(nums[i]);
                set.add(i);
                backtracking(nums, solution);
                solution.remove(solution.size() - 1);
                set.remove(i);
            }
        }
    }
}
```

[Permutations II](https://leetcode.com/problems/permutations-ii/> <br>
思路：回溯，冲突的发生除了index相同还有值相同。(我自己的想法有点问题, 必须要先排序)
```Java
class Solution {
    List<List<Integer>> res;
    Set<Integer> set;
    public List<List<Integer>> permute(int[] nums) {
        Arrays.sort(nums);
        res = new ArrayList<>();
        set = new HashSet<>();
        backtracking(nums, new ArrayList<>());
        return res;
    }
    
    private void backtracking(int[] nums, List<Integer> solution) {
        if (solution.size() == nums.length) {
            res.add(new ArrayList<>(solution));
            return;
        }
        
        for (int i = 0; i < nums.length; i++) {
            if (!set.contains(i)) {
                if (i > 0 && nums[i] == nums[i - 1] && !set.contains(i - 1)) {
                    continue;
                }
                solution.add(nums[i]);
                set.add(i);
                backtracking(nums, solution);
                solution.remove(solution.size() - 1);
                set.remove(i);
            }
        }
    }
}
```
[Generate Parentheses](https://leetcode.com/problems/generate-parentheses/) <br>
思路： 按照回溯的模板写就是下面这样, 我把选择列表先建起来方便理解，选择列表为左括号或右括号 （注意StringBuilder才是全局变量， 一个string + 另一个string其实会创建新的变量），注意常用函数，StringBuilder.deleteCharAt(). 当然可以用正常递归解。 <br>
问题的关键为定义level参数是什么。
```Java
class Solution {
    List<String> res;
    public List<String> generateParenthesis(int n) {
        //backtracking
        res = new ArrayList<>();
        StringBuilder curr = new StringBuilder();
        String[] options = new String[] {"(", ")"};
        backtracking(0, 0, n, curr, options);
        return res;
    }
    
    private void backtracking(int left, int right, int n, StringBuilder curr, String[] options) {
        if (left == n && right == n) {
            res.add(String.valueOf(curr));
        }
        
        for (String s : options) {
            if (left > n || left < right) {
                return;
            }
            if (s == "(") {
                curr.append("(");
                backtracking(left + 1, right, n, curr, options);
            } else {
                curr.append(")");
                backtracking(left, right + 1, n, curr, options);
            }
            curr.deleteCharAt(curr.length() - 1);
        }
    }
}
```
当使用String 而不是StringBuilder时，不需要回溯
```Java
class Solution {
    List<String> res;
    public List<String> generateParenthesis(int n) {
        //backtracking
        res = new ArrayList<>();
        String curr = "";
        String[] options = new String[] {"(", ")"};
        backtracking(0, 0, n, curr, options);
        return res;
    }
    
    private void backtracking(int left, int right, int n, String curr, String[] options) {
        if (left == n && right == n) {
            res.add(String.valueOf(curr));
        }
        
        for (String s : options) {
            if (left > n || left < right) {
                return;
            }
            if (s == "(") {
                backtracking(left + 1, right, n, curr + "(", options);
            } else {
                backtracking(left, right + 1, n, curr + ")", options);
            }
        }
    }
}
```

TO-DO： 查看 [回溯与不需要回溯的对比](https://leetcode-cn.com/problems/permutations/solution/hui-su-suan-fa-python-dai-ma-java-dai-ma-by-liweiw/) <br>


[Combination Sum](https://leetcode.com/problems/combination-sum/) <br>
这个题我就借用了unbounded 背包中的思路，没有用for循环，而是一种不选回头路的回溯, 以下两种写法做对比
```Java
class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> res = new ArrayList<>();
        backtracking(candidates, res, new ArrayList<>(), target, 0);
        return res;
    }
    
    private void backtracking(int[] candidates, List<List<Integer>> res, List<Integer> solution, int target, int level) {
        if (target == 0) {
            res.add(new ArrayList<>(solution));
            return;
        }
        
        if (level == candidates.length) {
            return;
        }
        
        if (candidates[level] <= target) {
            solution.add(candidates[level]);
            backtracking(candidates, res, solution, target - candidates[level], level);
            solution.remove(solution.size() - 1);
        }
        
        backtracking(candidates, res, solution, target, level + 1);
    }
}
```
```Java
class Solution {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        //backtracking
        List<List<Integer>> res = new ArrayList<>();
        backtracking(candidates, res, new ArrayList<>(), target, 0);
        return res;
    }
    
    private void backtracking(int[] candidates, List<List<Integer>> res, List<Integer> solution, int target, int level) {
        if (target == 0) {
            res.add(new ArrayList<>(solution));
            return;
        }
        
        if (level == candidates.length) {
            return;
        }
        
        for (int i = level; i < candidates.length; i++) {
            if (candidates[i] <= target) {
                solution.add(candidates[i]);
                backtracking(candidates, res, solution, target - candidates[i], i);
                solution.remove(solution.size() - 1);
            }
        }
    }
}
```

[Combination Sum II](https://leetcode.com/problems/combination-sum-ii/) <br>
和上一问一样的思路，又可以用两种方法解，太强了我
```Java
class Solution {
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        List<List<Integer>> res = new ArrayList<>();
        Arrays.sort(candidates);
        backtracking(candidates, res, new ArrayList<>(), target, 0);
        return res;
    }
    
    private void backtracking(int[] candidates, List<List<Integer>> res, List<Integer> solution, int target, int level) {
        if (target == 0) {
            res.add(new ArrayList<>(solution));
            return;
        }
        
        if (level == candidates.length) {
            return;
        }
        
        if (candidates[level] <= target) {
            solution.add(candidates[level]);
            backtracking(candidates, res, solution, target - candidates[level], level + 1);
            solution.remove(solution.size() - 1);
        }
        while (level + 1 < candidates.length && candidates[level + 1] == candidates[level]) {
            level++;
        }
        backtracking(candidates, res, solution, target, level + 1);
    }
}
```

```Java
class Solution {
    Set<Integer> set;
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        //backtracking
        List<List<Integer>> res = new ArrayList<>();
        Arrays.sort(candidates);
        set = new HashSet<>();
        backtracking(candidates, res, new ArrayList<>(), target, 0);
        return res;
    }
    
    private void backtracking(int[] candidates, List<List<Integer>> res, List<Integer> solution, int target, int level) {
        if (target == 0) {
            res.add(new ArrayList<>(solution));
            return;
        }
        
        if (level == candidates.length) {
            return;
        }
        
        for (int i = level; i < candidates.length; i++) {
            if (candidates[i] > target || (i > 0 && candidates[i] == candidates[i - 1] && !set.contains(candidates[i]))) {
                continue;
            }
            solution.add(candidates[i]);
            set.add(candidates[i]);
            backtracking(candidates, res, solution, target - candidates[i], i + 1);
            solution.remove(solution.size() - 1);
            set.remove(candidates[i]);
        }
    }
}
```
这个是上个版本的精简版，i > level去重用的非常精髓
```Java
class Solution {
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        List<List<Integer>> res = new ArrayList<>();
        Arrays.sort(candidates);
        backtracking(candidates, res, new ArrayList<>(), target, 0);
        return res;
    }
    
    private void backtracking(int[] candidates, List<List<Integer>> res, List<Integer> solution, int target, int level) {
        if (target == 0) {
            res.add(new ArrayList<>(solution));
            return;
        }
        
        if (level == candidates.length) {
            return;
        }
        
        for (int i = level; i < candidates.length; i++) {
            if (candidates[i] > target || (i > level && candidates[i] == candidates[i - 1])) { // i > level很强，非常强
                continue;
            }
            solution.add(candidates[i]);
            backtracking(candidates, res, solution, target - candidates[i], i + 1);
            solution.remove(solution.size() - 1);
        }
    }
}
```

[Restore IP Addresses](https://leetcode.com/problems/restore-ip-addresses/) <br>
这是道多参数的回溯，每一层的可选择的东西不同， 并且有层数和长度的双重限制。
```Java
class Solution {   
    List<String> res;
    public List<String> restoreIpAddresses(String s) {
        res = new ArrayList<>();
        dfs(s, 0, 0, new StringBuilder());
        return res;
    }
    
    private void dfs(String s, int segId, int segStart, StringBuilder curr) {
        if (segId == 4 || segStart == s.length()) {
            if (segId == 4 && segStart == s.length()) {
                curr.deleteCharAt(curr.length() - 1);
                res.add(curr.toString());
                curr.append('.');
            }
            return;
        }
        
        if (s.charAt(segStart)  == '0') {
            curr.append('0');
            curr.append('.');
            dfs(s, segId + 1, segStart + 1, curr);
            curr.setLength(curr.length() - 2);
        } else {
            int seg = 0;
            for (int i = 0; i < 3; i++) {
                int idx = segStart + i;
                if (idx < s.length()) {
                    seg = seg * 10 + (s.charAt(idx) - '0');
                    if (seg <= 255) {
                        curr.append(seg);
                        curr.append('.');
                        dfs(s, segId + 1, idx + 1, curr);
                        curr.setLength(curr.length() - (i + 2));
                    }
                }
            }
        }
    }
}
```

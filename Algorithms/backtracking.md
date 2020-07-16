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
思路： 回溯，记录已选择过的元素
```java
class Solution {
    List<List<Integer>> res;
    boolean[] mark;
    public List<List<Integer>> permute(int[] nums) {
        res = new ArrayList<>();
        mark = new boolean[nums.length];
        backtracking(nums, new ArrayList<>());
        return res;
    }
    
    private void backtracking(int[] nums, List<Integer> permu) {
        if (permu.size() == nums.length) {
            res.add(new ArrayList<>(permu));
            return;
        }
        
        for (int i = 0; i < nums.length; i++) {
            if (mark[i] == true) {
                continue;
            }
            permu.add(nums[i]);
            mark[i] = true;
            backtracking(nums, permu);
            permu.remove(permu.size() - 1);
            mark[i] = false;
        }
    }
}
```

[Permutations II](https://leetcode.com/problems/permutations-ii/> <br>
思路：回溯，同层剪枝问题
```Java
class Solution {
    List<List<Integer>> res;
    int depth;
    boolean[] check;
    public List<List<Integer>> permuteUnique(int[] nums) {
        Arrays.sort(nums);
        res = new ArrayList<>();
        check = new boolean[nums.length];
        backtracking(nums, new ArrayList<>(), 0);
        return res;
    }
    
    private void backtracking(int[] nums, List<Integer> permu, int depth) {
        if (permu.size() == nums.length) {
            res.add(new ArrayList<>(permu));
            return;
        }
        
        for (int i = 0; i < nums.length; i++) {
            if ((i > 0 && nums[i] == nums[i - 1] && check[i - 1] == false) || check[i] == true) { //注意剪枝的细节
                continue;
            }
            permu.add(nums[i]);
            check[i] = true;
            backtracking(nums, permu, depth + 1);
            permu.remove(permu.size() - 1);
            check[i] = false;
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



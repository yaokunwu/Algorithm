# 笔记
* 递归的时间复杂度计算 ：  带入例子 + 画递归树，state space tree。**  
* 递归的空间复杂度计算 ：  等于递归的深度
* IntelliJ 里面有个leetcode Plugin // TODO ： 加入这个plugin
* Home End 键， ctrl+左右，选单词，选整行 <br>
* * *
* * *
- - -
* **Important!!!** **写代码的习惯： newspaper metaphor : ！！！自顶向下的编程方式！！！ （像思考大层次的逻辑）** <br> 
思路和cs61B相同，一个homework或者lab会给定大框架，相当于给了主干逻辑, 学生需要做的就是填充某个function。
而自己写程序和刷题的时候没有大框架， 所以首先需要自己思考出大框架并确定，后续在fill out 具体的实现和function等。
```Java
class Solution {
    public boolean isPalindrome(String s) {
        // ***先思考并确定高层次（主干）逻辑***
        // 1. filter out number & char; 2. reverse and compare;

        String filteredS = _filterNonNumberAndChar(s);
        String reversedS = _reverseString(filteredS);
        return reversedS.equalsIgnoreCase(filteredS);
    }

    // 再进行fill out具体实现和function
    private String _reverseString(String filteredS) {
        return null;
    }
    private String _filterNonNumberAndChar(String s) {
        return null;
    }
}
```
* * *
* * *
- - -
* 实用小贴士 ： StringBuilder(string).reverse(); 

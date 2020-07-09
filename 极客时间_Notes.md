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

* 实用小贴士 ： StringBuilder(string).reverse(); 
* * *
* * *
- - -
## 第01课丨01数据结构与算法总览
* 需要建立脑图
数据结构三大类 <br>
1.一维数据结构 <br>
2.二维数据结构 <br>
3.特殊数据结构 <br>

算法 <br>
基本算法： <br>
1. if else <br>
2. while for loop <br>
3. recursion <br>
**找到重复单元是目标** <br>
高级算法 <br>
4. 搜索 （DFS， BFS， etc） <br>
5. 动态规划 <br>
6. 二分查找 <br>
7. 贪心 <br>
8. 数学， 几何 <br>
**TODO-绘制自己的脑图 （算法 + 数据结构）<br>

###联系方法
* 1. 5遍刷题法 <br>
* 2. 练习缺陷， 弱点地方 （走出舒适区） <br>

**5遍刷题法** 
单个题目的切题四件套 <br>
* Clarification <br>
* Possible solutions (所有的解法先过一遍，并比较时空复杂度，找到最优解法) <br>
* Coding  <br>
* Test cases <br>
刷题第一遍：<br>
* 5分钟: 读题+思考 <br>
* （如果不会）直接看解法： 注意！ 多解法，比较解法优劣<br>
* 背诵，默写 好的解法<br>
刷题第二遍：<br>
马上自己写 - Leetcode提交<br>
多种解法比较 体会 - 优化<br>
刷题第三遍：<br>
过了一天以后，再重复做题<br>
不同解法的熟练程度 - 弱的进行专项练习<br>
刷题第四遍<br>
过了一周之后，反复回来练习相同题目<br>
刷题第五遍<br>
面试前一周恢复性训练<br>

## Summary
* 职业训练： 拆分知识点，刻意练习，反馈<br>
* 五步刷题法<br>
* 做算法题的最大误区： 只做一遍<br>
**达到的目标：一看到题目就知道方法一是什么，二是什么，三是什么，马上把代码模板可以写出来，肌肉记忆<br>

* * *
* * *
- - -


# Binary
## Common coperations of binary
### Basic operations <br>
#### XOR operaton: 
* a = 0 ^ a = a ^ 0 <br>
* 0 = a ^ a;
* so that => a = a ^ b ^ b;
#### Switch two numbers <br>
a = a ^ b; <br>
b = a ^ b;  (b = a ^ b ^ b) <br>
a = a ^ b;  (a = a ^ b ^ a ^ b ^ b) <br>
#### Remove the last 1 <br>
a = n & (n - 1) <br>
Get the last 1 <br>
#### diff = n ^ (n & (n - 1)) <br>
#### Bitwise operation overview
Operations | Description | Rules 
--- | --- | --- 
& | and | 两个位都为1时，结果为1 
| | or | 两个位都为0时，结果为0
^ | Xor | 两个位相同为0，相异为1
~ | reverse | 0变1， 1变0
<< | shift left | 各二进位全部左移若干位，高位丢弃，低位补0
'>>' | shift right | 各二进位全部右移若干位，对无符号数，高位补0，有符号数，各编译器处理方法不一样，有的补符号位（算数右移），有的补0（逻辑右移）

[Single Number](https://leetcode.com/problems/single-number) <br>
思路：把所有数字Xor一遍，剩余的数字就是要找的数字。
```Java
class Solution {
    public int singleNumber(int[] nums) {
        // Validation check
        if (nums == null) {
            return -1;
        }
        //Xor every element in array, return the res;
        int res = 0;
        for (int element : nums) {
            res = element ^ res;
        }
        return res;
    }
}
```

[Single Number II](https://leetcode.com/problems/single-number-ii) <br>
思路： 哈希表计数，达到3移除，剩余为result
class Solution {
    //
    public int singleNumber(int[] nums) {
        int res = 0;
        Map<Integer,Integer> countMap = new HashMap<>();
        for (int element : nums) {
            countMap.put(element, countMap.getOrDefault(element, 0) + 1);
            if (countMap.get(element) == 3) {
                countMap.remove(element);
            }
        }
        for (int key : countMap.keySet()) {
            res = key;
        }
        return res;
    }
}
```
位运算思路： 有点难理解，先放到TO-Do List里面


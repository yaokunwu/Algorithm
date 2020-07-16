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
'>>' | shift right | 各二进位全部右移若干位，对无符号数，高位补0，有符号数，补1（逻辑右移）
'>>>' | shift right | 各二进位全部右移若干位， 逻辑右移， 高位补0）

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
位运算思路： 有点难理解，先放到TO-Do List里
**TO - DO: single number系列**

[Number of 1 Bits](https://leetcode.com/problems/number-of-1-bits/) <br>
思路： 用 n & (n - 1)来移除最后一个1，直到n达到0为止，那么，1的个数就是移除的次数。
```Java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int res = 0;
        while (n != 0) {
            res++;
            n = n & (n - 1);
        }
        return res;
    }
}
```

[Counting Bits](https://leetcode.com/problems/counting-bits/) <br>
思路1: 跟上题差不多，只是需要计算所有value的1的个数，要熟记 n & (n -1)会移除最后一位。
```Java
class Solution {
    public int[] countBits(int num) {
        int[] res = new int[num + 1];
        for (int i = 0; i <= num; i++) {
            int count = 0;
            int currValue = i;
            while (currValue != 0) {
                count++;
                currValue &= currValue - 1;
            }
            res[i] = count;
        }
        return res;
    }
}
```
思路2： 最后设置位：就是最后一个1的位置。动规递推公式： dp(n) = dp(n & n -1) + 1; (就是去掉最后一位时的1的个数加上一就是当前数字）
```Java
class Solution {
    public int[] countBits(int num) {
        int[] res = new int[num + 1];
        res[0] = 0;
        for (int i = 1; i <= num; i++) {
            res[i] = res[i & (i - 1)] + 1;
        }
        return res;
    }
}
```

[Reverse Bits](https://leetcode.com/problems/reverse-bits/) <br>
思路：逐位移动，再逐位合并， **注意**： >>> 和 >> 的区别。。 >>> 补0, >> 正数补零，负数补1
```Java
public class Solution {
    // you need treat n as an unsigned value
    public int reverseBits(int n) {
        int i = 0;
        int res = 0;
        while (n != 0) {
            int curr = (n & 1) << (31 - i);
            res |= curr;
            n = n >>> 1;
            i++;
        }
        return res;
    }
}
```

[Bitwise AND of Numbers Range](https://leetcode.com/problems/bitwise-and-of-numbers-range/) <br>
思路： 问题转化为找两个整数的公共前缀 (注意： 左移 << 只有这一种形式，低位补0)
```Java
class Solution {
    public int rangeBitwiseAnd(int m, int n) {
        int count = 0;
        while (m != n) {
            m = m >>> 1;
            n = n >>> 1;
            count++;
        }
        return m << count;
    }
}
```

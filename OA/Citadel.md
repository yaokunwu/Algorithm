OA: [dropRequest](https://leetcode.com/discuss/interview-question/819577/Throttling-Gateway-Hackerrank)
```Java
public int droppedRequest(int n, int[] requestTime) {
    int count = 0;
    int start = 0;
    int countTen = 0;
    int countMin = 0;
    int countSec = 0;
    int end = 0;
    while (end < requestTime.length) {
        if (requestTime[start] < Math.max(60, requestTime[end]) - 59) {
            start++;
            countMin--;
        } else if (requestTime[start] < Math.max(10, requestTime[end]) - 9) {
            start++;
            countTen--;
        } else {
            countTen++;
            countMin++;
            countSec++;
            if (countMin > 60) {
                count++;
            } else if (countTen > 20) {
                count++;
            } else if (countSec > 3) {
                count++;
            }
            if (end < requestTime.length - 1 && requestTime[end] != requestTime[end + 1]) {
                countSec = 0;
            }
            end++;
        }
    }
    return count;
}
```

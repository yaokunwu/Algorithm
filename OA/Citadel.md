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
/*** Bid easy ***/
```Java
public List<Integer> ipo(int[][] bids, int shares) {
    List<Integer> res = new ArrayList<>();
    bids = mergeSort(bids);
    for (int i = 0; i < bids.length; i++) {
        if (shares > 0) {
            shares -= bids[i][1];
        } else {
            res.add(bids[i][0]);
        }
    }
    return res;
}

private int[][] mergeSort(int[][] bids) {
    if (bids.length <= 1) {
        return bids;
    }
    int middle = bids.length / 2;
    int[][] leftArray = mergeSort(Arrays.copyOfRange(bids, 0, middle));
    int[][] rightArray = mergeSort(Arrays.copyOfRange(bids, middle, bids.length));
    return merge(leftArray, rightArray);
}

private int[][] merge(int[][] left, int[][] right) {
    int[][] merged = new int[left.length + right.length][4];
    int i = 0, j = 0, k = 0;

    while (i < left.length && j < right.length) {
        if (left[i][2] > right[j][2] || (left[i][2] == right[j][2] && left[i][3] < right[j][3])) {
            merged[k++] = left[i++];
        } else {
            merged[k++] = right[j++];
        }
    }

    while (i < left.length) {
        merged[k++] = left[i++];
    }
    while (j < right.length) {
        merged[k++] = right[j++];
    }

    return merged;
}
    
Bid original
```Java
 /*** Bid original ***/
public List<Integer> ipo_original(int[][] bids, int shares) {
    List<Integer> res = new ArrayList<>();
    bids = mergeSort(bids);
    int countMember = 0;
    int countTotal = 0;
    int i = 0;
    while (i < bids.length) {
        countMember++;
        if (countMember > shares) {
            break;
        }
        countTotal += bids[i][1];
        if ((i == bids.length - 1) || (i < bids.length - 1 && bids[i][2] != bids[i + 1][2])) {
            if (shares > countTotal) {
                shares = shares - countTotal;
            } else {
                i++;
                break;
            }
            countTotal = 0;
            countMember = 0;
        }
        i++;
    }
    while (i < bids.length) {
        res.add(bids[i][0]);
        i++;
    }
    return res;
}
```
/*** Matrix sum  ***/
```Java

public int[][] matrixSum(int[][] after) {
    int rows = after.length;
    int cols = after[0].length;
    for (int row = rows - 1; row >= 0; row--) {
        for (int col  = cols - 1; col >= 0; col--) {
            int left = 0, up = 0, leftup = 0;
            if (col - 1 >= 0 && row - 1 >= 0) {
                leftup = after[row - 1][col - 1];
            }
            if (col - 1 >= 0) {
                left = after[row][col - 1];
            }
            if (row - 1 >= 0) {
                up = after[row - 1][col];
            }
            after[row][col] = after[row][col] - left - up + leftup;
        }
    }
    return after;
}
```
/*** Consecutive Sum  ***/
```Java
public int consecutiveNumbersSum(long N) {
    N = N - 1;
    int res = 0, i = 2;
    while (N > 0) {
        if (N % i == 0) {
            res++;
        }
        N -= i;
        i++;
    }
    return res;
}
```

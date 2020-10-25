/*** simple cipher ***/

```Java
public static String simpleCipher(String encrypted, int k) {
    StringBuilder sb = new StringBuilder();
    char[] s = encrypted.toCharArray();
    int n = s.length;
    k = k % 26;
    for (int i = 0; i < n; i++) {
        int currIdx = s[i] - 'A';
        int newIdx = currIdx - k >= 0 ? currIdx - k : currIdx - k + 26;
        sb.append((char) (newIdx + 'A'));
    }
    return sb.toString();
}
```

/*** maxProfit ***/

```Java
public static int maxProfit(int k, int[] profit) {
    int n = profit.length;
    int res = Integer.MIN_VALUE;
    int interval = n / 2;
    int sum = 0;
    int start = 0;
    for (int i = 0; i - k + 1 + interval < n; i++) {
        sum += profit[i];
        sum += profit[(i + interval) % n];
        if (i >= k - 1) {
            res = Math.max(res, sum);
            if (i - k + 1 + interval == n - 1) {
                break;
            }
            sum -= profit[start];
            sum -= profit[(start + interval) % n];
            start++;
        }
    }
    return res;
}
```

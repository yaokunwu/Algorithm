
/** commonElementsInArrays **/
```Java
public static List<Integer> commonElementsInArrays(List<Integer> arr1, List<Integer> arr2) {
    Map<Integer, Integer> count1 = new HashMap<>();
    Map<Integer, Integer> count2 = new HashMap<>();
    int m = arr1.size();
    int n = arr2.size();
    for (int i = 0; i < m; i++) {
        count1.put(arr1.get(i), count1.getOrDefault(arr1.get(i), 0) + 1);
    }
    for (int i = 0; i < n; i++) {
        count2.put(arr2.get(i), count2.getOrDefault(arr2.get(i), 0) + 1);
    }
    List<Integer> res = new ArrayList<>();
    for (int num = 0; num <= 1000; num++) {
        if (count1.containsKey(num) && count2.containsKey(num)) {
            int min = Math.min(count1.get(num), count2.get(num));
            for (int i = 0; i < min; i++) {
                res.add(num);
            }
        }
    }
    return res;
}
```

```Java
/** Real Programmer Game **/
public static double realProgrammerGame(int N, int M, int K) {
    long countLessThan = 0;
    long[][] f = kways(M, K, N - 1);
    for (int i = 0; i < N; i++) {
        countLessThan += f[i][K];
    }
    long totalCount = (long) Math.pow(M + 1, K);
    return (double) (totalCount - countLessThan) / totalCount;
}

public static long[][] kways(int M, int K, int target) {
    // how many K size combination that sums up to target
    // state f[i][k] represent how many ways to obtain the target i with k size. return f[target][K];
    // state tranfer: f[i][k] = for all num in arr, f[i - num][k - 1];
    // init: f[0][0] = 1
    long[][] f = new long[target + 1][K + 1];
    f[0][0] = 1;
    for (int i = 0; i <= target; i++) {
        for (int k = 0; k <= K; k++) {
            for (int num = 0; num <= M; num++) {
                if (i >= num && k > 0) {
                    f[i][k] += f[i - num][k - 1];
                }
            }
        }
    }
    return f;
}
```

```Java
/** Incremental Memory Leak **/
public static List<List<Integer>> incrementalMemoryLeak(List<List<Long>> input) {
    int n = input.size();
    List<List<Integer>> res = new ArrayList<>();
    for (int i = 0; i < n; i++) {
        res.add(compute(input.get(i)));
    }
    return res;
}

private static List<Integer> compute(List<Long> input) {
    long m1 = input.get(0);
    long m2 = input.get(1);
    List<Integer> res = new ArrayList<>();
    int i = 1;
    while (true) {
        if (m1 >= m2) {
            if (m1 < i) {
                break;
            }
            m1 -= i;
        } else {
            if (m2 < i) {
                break;
            }
            m2 -= i;
        }
        i++;
    }
    res.add(i);
    res.add((int) m1);
    res.add((int) m2);
    return res;
}
```
```Java
/** minimum character transformation **/
public static int minimumCharacterTransformation(String str1, String str2) {
    char[] s1 = str1.toCharArray();
    char[] s2 = str2.toCharArray();
    Set<Character> visited = new HashSet<>();
    Map<Character, Character> map = new HashMap<>();
    int n = s1.length;
    for (int i = 0; i < n; i++) {
        if (map.containsKey(s1[i]) && map.get(s1[i]) != s2[i]) {
            return -1;
        }
        if (s1[i] != s2[i]) {
            map.put(s1[i], s2[i]);
        }
    }
    int res = 0;
    for (int i = 0; i < n; i++) {
        char curr = s1[i];
        if (!visited.contains(curr)) {
            Set<Character> currSet = new HashSet<>();
            boolean cycleFound = mctdfs(s1[i], map, currSet, visited);
            if (cycleFound) {
                res++;
            }
            visited.addAll(currSet);
            res += currSet.size();
        }
    }
    return res;
}

private static boolean mctdfs(char currChar, Map<Character, Character> map, Set<Character> currSet, Set<Character> visited) {
    if (visited.contains(currChar)) {
        return false;
    }
    if (currSet.contains(currChar)) {
        return true;
    }
    currSet.add(currChar);
    return mctdfs(map.get(currChar), map, currSet, visited);
}
```

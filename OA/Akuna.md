/** Akuna **/ <br>
/*** Divisibility of strings ***/
```Java
public static int divisibleString(String s, String t) {
    System.out.println(s.length() % t.length());
    if (!check(s, t)) {
        return -1;
    }
    return minLen(t);
}

private static int minLen(String s) {
    int n = s.length();
    for (int i = 1; i <= n / 2; i++) {
        if (n % i == 0) {
            if (check(s, s.substring(0, i))) {
                return i;
            }
        }
    }
    return n;
}

private static boolean check(String s, String t) {
    int m = s.length();
    int n = t.length();
    if (m % n != 0) {
        return false;
    }
    int i = 0;
    while (i < m) {
        if (!s.substring(i, i + n).equals(t)) {
            return false;
        }
        i += n;
    }
    return true;
}
```
/*** Binary complete ***/

```Java
public static List<Integer> binaryComplete(List<String> commands) {
    Map<String, Integer> map = new HashMap<>();
    List<Integer> res = new ArrayList<>();
    int n = commands.size();
    int i, j;
    for (i = 0; i < n; i++) {
        String command = commands.get(i);
        boolean found = false;
        for (j = command.length(); j >= 1; j--) {
            String key = command.substring(0, j);
            if (!found && map.containsKey(key)) {
                res.add(map.get(key));
                found = true;
            }
            map.put(key, i + 1);
        }
        if (!found) {
            res.add(i);
        }
    }
    return res;
}
```

/*** Grouping options ***/
错误思路
//State: f[i][j] represent how many ways for previous i group with size of j
//State transfer: f[i][j] = forAll k <= j sum(f[i - 1][j - k])
//f[0][0] = 1 
```Java
static int res;
public static int groupingOption(int people, int groups) {
    dfs(0, 1, 0);
    return res;
}

private void dfs(int level, int l, int total) {
    if (level == groups) {
        if (total == people) {
            res++;
        }
        return;
    }
    
    for (int i = l; i <= total; i++) {
        dfs(level + 1, i, total + i);
    }
}
```
正确思路
//State: f[i][j][k] represent how many ways for previous i group with size j and the size of group i - 1 is k
//State transfer: f[i][j][k] = forAll prevSize <= k, sum(f[i - 1][j - k][prevSize])
//f[0][0][0] = 1;
```Java
public static int groupingOptionDp(int people, int groups) {
    int[][][] f = new int[groups + 1][people + 1][people + 1];
    int i, j, k, preSize;
    f[0][0][0] = 1;
    for (i = 1; i <= groups; i++) {
        for (j = 1; j <= people; j++) {
            for (k = 1; k <= j; k++) {
                f[i][j][k] = 0;
                for (preSize = 0; preSize <= k; preSize++) {
                    f[i][j][k] += f[i - 1][j - k][preSize];
                }
            }
        }
    }
    int res = 0;
    for (i = 0; i <= people; i++) {
        res += f[groups][people][i];
    }
    return res;
}
```

/*** disk space analysis ***/

```Java
public static int segment(int x, List<Integer> space) {
    int n = space.size();
    int res = Integer.MIN_VALUE;
    Deque<Integer> deque = new LinkedList<>();
    int i;
    int start = 0;
    for (i = 0; i < space.size(); i++) {
        while (!deque.isEmpty() && space.get(deque.getLast()) > space.get(i)) {
            deque.removeLast();
        }
        deque.add(i);
        if (i >= x - 1) {
            res = Math.max(res, space.get(deque.getFirst()));
            if (deque.getFirst() == start) {
                deque.removeFirst();
            }
            start++;
        }
    }
    return res;
}
```













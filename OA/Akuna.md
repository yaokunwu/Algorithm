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

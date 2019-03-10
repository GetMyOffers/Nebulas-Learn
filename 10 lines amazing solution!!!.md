### 10 lines amazing solution!!!


We can easily observe below:

```
5 * 4 / 3 = 6
6 * 5 / 4 = 7
10 * 9 / 8 = 11
...
...
...

```

so we can get this formula: `i * (i-1) / (i-1) = i+1` when `i >= 5`

so `i * (i-1) / (i-2) + (i-3) = 2 * i - 2`

we can simplify our computation as below:

```
    i * (i-1) / (i-2) + (i-3) - (i-4) * ....
=   2 * i - 2 - (i-4) * ....
=   2 * i - 2 - (2 * (i-4) - 2) ...
=   
```

we can call each 4 numbers a `chunk`, so from `N % 4` we can know how many chunks there are, then the rest 
`0`, `1`, `3` and `4` elements will influence our final result.

After consider the corner case, we can arrive the solution:

```
class Solution:
    def clumsy(self, N: int) -> int:
        if N <= 2:
            return N
        if N <= 4:
            return N + 3
        
        if (N - 4) % 4 == 0:
            return N + 1
        elif (N - 4) % 4 <= 2:
            return N + 2
        else:
            return N - 1
```



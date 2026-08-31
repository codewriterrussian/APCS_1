# APCS / ZeroJudge 五題 Python 解法整理

## 題目列表

| 題目編號 | 題目 | 配分 |
|---|---|---:|
| 第 1 題 | [c295. APCS-2016-1029-2 最大和](https://zerojudge.tw/ShowProblem?problemid=c295) | 20/100 |
| 第 2 題 | [c291. APCS 2017-0304-2 小群體](https://zerojudge.tw/ShowProblem?problemid=c291) | 20/100 |
| 第 3 題 | [c462. APCS 交錯字串](https://zerojudge.tw/ShowProblem?problemid=c462) | 20/100 |
| 第 4 題 | [q182. 字串操作](https://zerojudge.tw/ShowProblem?problemid=q182) | 20/100 |
| 第 5 題 | [b965. 矩陣轉換](https://zerojudge.tw/ShowProblem?problemid=b965) | 20/100 |

---

# 第 1 題：c295 最大和

題目要求每一群選一個數字，使總和最大。

因此每一列直接選最大值，再把這些最大值加起來。最後檢查哪些被選到的數字可以整除總和 `S`。

## 解題思路

例如：

```text
1 5      → 選 5
6 4      → 選 6
1 1      → 選 1
```

所以：

```text
S = 5 + 6 + 1 = 12
```

再檢查：

```text
12 % 5 != 0
12 % 6 == 0
12 % 1 == 0
```

答案：

```text
12
6 1
```

## Python

```python
N, M = [int(x) for x in input().split()]

chosen = []

for i in range(N):
    row = [int(x) for x in input().split()]
    chosen.append(max(row))

S = sum(chosen)

print(S)

answer = []

for x in chosen:
    if S % x == 0:
        answer.append(str(x))

if len(answer) == 0:
    print(-1)
else:
    print(" ".join(answer))
```

## 時間複雜度

```text
O(N × M)
```

---

# 第 2 題：c291 小群體

每個人只有一個最好朋友，而且 `0 ~ N-1` 每個好友編號恰好出現一次，所以整個好友關係會形成數個獨立的環。

只要從尚未拜訪的人開始一直追蹤朋友，直到走到已拜訪的人，就找到一個小群體。

例如：

```text
0 → 4 → 6 → 8 → 5 → 0
```

是一群。

```text
1 → 7 → 1
```

又是一群。

## Python

```python
N = int(input())

friend = [int(x) for x in input().split()]

visited = [False] * N

groups = 0

for i in range(N):

    if visited[i] == False:

        groups += 1

        current = i

        while visited[current] == False:

            visited[current] = True

            current = friend[current]

print(groups)
```

以題目範例：

```text
10
4 7 2 9 6 0 8 1 5 3
```

會找到：

```text
0 → 4 → 6 → 8 → 5 → 0
1 → 7 → 1
2 → 2
3 → 9 → 3
```

所以：

```text
4
```

## 時間複雜度

```text
O(N)
```

因為每個人最多真正拜訪一次。

---

# 第 3 題：c462 交錯字串

`k-交錯字串` 的意思是：

- 連續 `k` 個大寫
- 接著連續 `k` 個小寫
- 再連續 `k` 個大寫
- 如此交錯

也可以從小寫開始。

題目要求找最長的連續子字串。

例如 `k = 2`：

```text
aaBBccDD
```

是合法的：

```text
aa | BB | cc | DD
```

長度：

```text
8
```

## 先把字串變成「連續大小寫長度」

例如：

```text
aafAXbbCDCCC
```

大小寫分組：

```text
aaf | AX | bb | CDCCC
```

長度：

```text
3 2 2 5
```

當：

```text
k = 2
```

可以取：

```text
af | AX | bb | CD
```

也就是：

```text
2 + 2 + 2 + 2 = 8
```

## 一個重要規則

如果某一組長度：

```text
< k
```

完全不能使用。

如果：

```text
== k
```

可以出現在交錯字串的任何位置。

如果：

```text
> k
```

只能當交錯字串的開頭或結尾。

例如 `k = 2`：

```text
aaa | BB | ccc
```

可以取：

```text
aa | BB | cc
```

長度為 `6`。

但是：

```text
aa | BBB | cc
```

中間 `BBB` 有 3 個大寫，不能直接跨過去形成三組完整區塊。

## Python

```python
k = int(input())

s = input()

runs = []

count = 1

for i in range(1, len(s)):

    if s[i].isupper() == s[i - 1].isupper():

        count += 1

    else:

        runs.append(count)
        count = 1

runs.append(count)


current = 0
best = 0

for length in runs:

    if length < k:

        current = 0

    elif length == k:

        current += 1

        if current > best:
            best = current

    else:

        # length > k
        # 可以當目前交錯字串的最後一組
        if current + 1 > best:
            best = current + 1

        # 但不能成為下一段的中間組
        # 所以只能重新把自己當成第一組
        current = 1

print(best * k)
```

範例：

```text
2
aafAXbbCDCCC
```

分組：

```text
3 2 2 5
```

得到：

```text
8
```

## 時間複雜度

```text
O(N)
```

---

# 第 4 題：q182 字串操作

有三種操作：

```text
0 → 兩兩交換
1 → 兩兩排序
2 → 完美重排
```

## 操作 0：兩兩交換

例如：

```text
apcsntnu
```

分組：

```text
(ap)(cs)(nt)(nu)
```

交換：

```text
(pa)(sc)(tn)(un)
```

得到：

```text
pasctnun
```

## 操作 1：兩兩排序

例如：

```text
family
```

```text
(fa)(mi)(ly)
```

每一組按照字典順序：

```text
(af)(im)(ly)
```

得到：

```text
afimly
```

## 操作 2：完美重排

例如：

```text
apcsntnu
```

切成：

```text
apcs
ntnu
```

然後交錯：

```text
a n p t c n s u
```

得到：

```text
anptcnsu
```

## Python

```python
s = input()

k = int(input())

for i in range(k):

    operation = int(input())

    # 0：兩兩交換
    if operation == 0:

        result = ""

        for j in range(0, len(s), 2):

            result += s[j + 1]
            result += s[j]

        s = result

    # 1：兩兩排序
    elif operation == 1:

        result = ""

        for j in range(0, len(s), 2):

            a = s[j]
            b = s[j + 1]

            if a > b:
                a, b = b, a

            result += a
            result += b

        s = result

    # 2：完美重排
    else:

        result = ""

        half = len(s) // 2

        for j in range(half):

            result += s[j]
            result += s[half + j]

        s = result

print(s)
```

## 時間複雜度

```text
O(k × |S|)
```

---

# 第 5 題：b965 矩陣轉換

這題最重要的是看清楚：

> 題目給的是最後的矩陣 **B**，要你找原本的 **A**。

A 經過：

```text
操作 1
↓
操作 2
↓
操作 3
↓
B
```

要找回 A，就必須：

```text
B
↓
操作 3 的反操作
↓
操作 2 的反操作
↓
操作 1 的反操作
↓
A
```

因此一定要：

```python
reversed(operations)
```

題目定義：

```text
0 → 順時針旋轉 90°
1 → 上下翻轉
```

---

## 操作 1：翻轉

翻轉本身就是自己的反操作。

例如：

```text
1 2
3 4
5 6
```

上下翻：

```text
5 6
3 4
1 2
```

再翻一次就恢復。

Python：

```python
matrix = matrix[::-1]
```

---

## 操作 0：順時針旋轉

原本 A → B 是：

```text
順時針 90°
```

因此 B → A 必須：

```text
逆時針 90°
```

例如：

```text
1 2 3
4 5 6
```

逆時針後：

```text
3 6
2 5
1 4
```

Python 可以：

```python
matrix = [list(row) for row in zip(*matrix)][::-1]
```

---

## 完整 Python

```python
R, C, M = [int(x) for x in input().split()]

matrix = []

for i in range(R):
    row = [int(x) for x in input().split()]
    matrix.append(row)

operations = [int(x) for x in input().split()]


# 因為是從 B 找回 A
# 所以操作順序反過來
for operation in reversed(operations):

    # 原操作 1：上下翻轉
    # 翻轉自己的反操作仍然是翻轉
    if operation == 1:

        matrix = matrix[::-1]

    # 原操作 0：順時針 90 度
    # 反操作是逆時針 90 度
    else:

        matrix = [
            list(row)
            for row in zip(*matrix)
        ][::-1]


R = len(matrix)
C = len(matrix[0])

print(R, C)

for row in matrix:
    print(" ".join(str(x) for x in row))
```

以範例：

```text
3 2 3
1 1
3 1
1 2
1 0 0
```

輸出：

```text
3 2
1 1
1 3
2 1
```

---

# 五題重點整理

| 題目 | 核心技巧 | 難度 |
|---|---|---|
| c295 最大和 | `max()`、串列、整除 | ★ |
| c291 小群體 | `visited`、循環走訪 | ★★ |
| c462 交錯字串 | 字串分段、連續區間 | ★★★ |
| q182 字串操作 | 字串模擬 | ★★ |
| b965 矩陣轉換 | 二維陣列、逆操作 | ★★★ |

其中 **c462 與 b965 最值得仔細理解**；前兩題非常適合練習 APCS 的基本陣列走訪，第 4 題則是很典型的模擬題。

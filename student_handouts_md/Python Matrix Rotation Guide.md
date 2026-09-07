# Python `zip()` 與矩陣旋轉

## 1. `zip()` 是什麼？

`zip()` 可以把多個串列中，相同位置的元素配對在一起。

例如：

```python
a = [1, 2, 3]
b = ["A", "B", "C"]

print(list(zip(a, b)))
```

結果：

```text
[(1, 'A'), (2, 'B'), (3, 'C')]
```

---

## 2. `zip(*matrix)` 是什麼？

假設有一個二維矩陣：

```python
matrix = [
    [1, 2, 3],
    [4, 5, 6]
]
```

`*matrix` 會把每一列拆開：

```python
zip(*matrix)
```

相當於：

```python
zip(
    [1, 2, 3],
    [4, 5, 6]
)
```

所以：

```python
list(zip(*matrix))
```

結果會是：

```text
[(1, 4), (2, 5), (3, 6)]
```

也就是把原本的「列」變成「行」。

原本：

```text
1 2 3
4 5 6
```

轉置後：

```text
1 4
2 5
3 6
```

如果希望每一列仍然是 `list`：

```python
matrix = [
    list(row)
    for row in zip(*matrix)
]
```

---

# 3. 用 `zip()` 做矩陣旋轉

假設：

```text
1 2 3
4 5 6
```

先做：

```python
[
    list(row)
    for row in zip(*matrix)
]
```

得到：

```text
1 4
2 5
3 6
```

再把上下順序反過來：

```python
[::-1]
```

得到：

```text
3 6
2 5
1 4
```

這就是原矩陣的 **逆時針旋轉 90 度**。

因此可以寫成：

```python
matrix = [
    list(row)
    for row in zip(*matrix)
][::-1]
```

---

# 4. 題目完整程式

這題是從最後的矩陣 `B` 找回原本的矩陣 `A`。

因此：

1. 操作順序要反過來。
2. 每個操作也要使用它的反操作。

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

---
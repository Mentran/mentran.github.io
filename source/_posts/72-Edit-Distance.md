title: 72. Edit Distance
author: Mentran
tags:
  - dp
  - edit distance
  - python
  - code
categories:
  - leetcode
date: 2019-08-07 15:45:00
---
### 题目
给定两个单词 word1 和 word2，计算出将 word1 转换成 word2 所使用的最少操作数 。

你可以对一个单词进行如下三种操作：

插入一个字符
删除一个字符
替换一个字符

示例 1:
```
输入: word1 = "horse", word2 = "ros"
输出: 3
解释: 
horse -> rorse (将 'h' 替换为 'r')
rorse -> rose (删除 'r')
rose -> ros (删除 'e')
```
示例 2:
```
输入: word1 = "intention", word2 = "execution"
输出: 5
解释: 
intention -> inention (删除 't')
inention -> enention (将 'i' 替换为 'e')
enention -> exention (将 'n' 替换为 'x')
exention -> exection (将 'n' 替换为 'c')
exection -> execution (插入 'u')
```
来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/edit-distance
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

---

<!-- more -->

### 思路
1. **DP** 问题

2. 考虑状态变化
>`dp[i][j]` 有3种变化
>* `dp[i-1][j-1]` : `word1[i]` 修改为 `word2[j]`，注意当`word1[i]==word2[j]`时，编辑距离不变，否则编辑距离+1
>* `dp[i-1][j]` : `word1` 插入字符 或 `word2[j]` 删除字符，编辑距离+1
>* `dp[i][j-1]` : `word1` 删除字符 或 `word2[j]` 插入字符，编辑距离+1

3. 状态转移方程
```python
dp[i][j] = min(dp[i-1][j-1] + (word1[i]!=word2[j]), dp[i-1][j] + 1, dp[i][j-1] + 1)
```

4. 考虑初始状态

### 代码
```python
def minDistance(word1: str, word2: str) -> int:
    l1, l2 = len(word1), len(word2)
    dp = [[0] * (l2 + 1) for _ in range(l1 + 1)]

    for i in range(l1 + 1):
        dp[i][0] = i
    for j in range(l2 + 1):
        dp[0][j] = j

    for i in range(1, l1 + 1):
        for j in range(1, l2 + 1):
            dp[i][j] = min(dp[i - 1][j - 1] + (word1[i - 1] != word2[j - 1]), dp[i - 1][j] + 1, dp[i][j - 1] + 1)

    return dp[l1][l2]
```
#### 空间优化
观察状态转移方程可以发现，`dp[i][j]` 的值只跟比`(i, j)`小的值有关，即可重复使用上一步的结果
* 可增加1行保存上一层的结果
* 可增加1个变量记录每次被更改的 `dp[i-1][j-1]`
```python
def minDistance(word1: str, word2: str) -> int:
    l1, l2 = len(word1), len(word2)
    dp = [0] * (l2 + 1)

    for j in range(l2 + 1):
        dp[j] = j

    for i in range(1, l1 + 1):
        old_dp = dp[0]
        dp[0] = i
        for j in range(1, l2 + 1):
            tmp = dp[j]
            dp[j] = min(old_dp + (word1[i - 1] != word2[j - 1]), dp[j] + 1, dp[j - 1] + 1)
            old_dp = tmp

    return dp[l2]
```
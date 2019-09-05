---
layout:     post                    # 使用的布局（不需要改）
title:      LeetCode 552, 564, 956题解        # 标题 
subtitle:   LeetCode 题解 Vol.1      #副标题
date:       2019-09-04              # 时间
author:     holynova                     # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - leetcode
---

## LeetCode 552.学生出勤记录Ⅱ

####题目大意：
给定一个字符串来代表一个学生的出勤记录，这个记录仅包含以下三个字符：
   1.'A':Absent,缺勤
   2.'L':Late,迟到
   3.'P':Present,到场
如果一个学生的出勤记录中不超过一个'A'并且不超过两个连续'L'，那么这个学生会被奖赏。
给定一个正整数 n (n小于100000)，返回所有可被视为可奖励的出勤记录的数量。答案可能非常大，只需返回结果 mod 1000000007的值。

示例：
   **输入：** n = 2
   **输出：** n = 8
   **解释：** 
   有8个长度为2的记录将被视为可奖励：
   “PP”，“AP”，“PA”，“LP”，“PL”，“AL”，“LA”，“LL”
   只有“AA”不会被视为可奖励，因为缺勤次数超过一次。

####题解：
这道题使用动态规划求解，主要着眼于状态的定义。定义 dp[m][i][j] 为前m次出勤中，出勤记录里有 i 个 'A' 并且末尾有 j 个连续 'L' 的可奖励的出勤记录的数量。显然 i 只能取 0 或 1 ，j 只能取 0, 1 或 2 ，一共 2 × 3 = 6 种情况。从 m 次到 m + 1 次，转移方程分别列出如下：
   dp[m + 1][0][0] = SUM(dp[m][0])  (三个元素的和)
   dp[m + 1][0][1] = dp[m][0][0]
   dp[m + 1][0][2] = dp[m][0][1]
   dp[m + 1][1][0] = SUM(dp[m])   (上一层六个元素的和)
   dp[m + 1][1][1] = dp[m][1][0]
   dp[m + 1][1][2] = dp[m][1][1]
并且第 m + 1 层只依赖于第 m 层，所以可以靠单独设置几个变量将最外面的一层去掉，将数组变为dp[i][j]。
一直推导到最后，将最后一步得到的dp数组六个值求和便是结果。

####代码：
```
class Solution {
public:
    int checkRecord(int n) {

		if (n == 1) {
			return 3;
		}

		int dp[2][3] = {{2, 1, 1}, {3, 1, 0}}, ans = 0, mod = 1000000007;
		n -= 2;
		while (n--) {
			int tmp00 = dp[0][0];
			int tmp10 = dp[1][0];
			int next01 = 0;
			for (int i = 0; i < 6; ++i) {
				next01 += dp[i / 3][i % 3];
				next01 %= mod;
			}
			dp[1][0] = next01;
			dp[1][2] = dp[1][1];
			dp[1][1] = tmp10;
			dp[0][0] = (dp[0][0] + dp[0][1]) % mod;
			dp[0][0] = (dp[0][0] + dp[0][2]) % mod;
			dp[0][2] = dp[0][1];
			dp[0][1] = tmp00;
		}

		for (int i = 0; i < 6; ++i) {
			ans += dp[i / 3][i % 3];
			ans %= mod;
		}

		return ans;
    }
};
```

##LeetCode 564.寻找最近的回文数

####题目大意：
给定一个整数n，你需要找到与它最近的回文数，不包括自身。
“最近的”定义为两个整数绝对值差最小。

示例：
   **输入：** “123”
   **输出：** “121”

注意：
   1.n 是由字符串表示的整数，其长度不超过18.
   2.如果有多个结果，返回最小的那个。


####题解：
这个题目首先最容易想到的就是直接使用输入串的前半部分构造一个回文串来作为结果。这没有问题，确实是一种解。那么暂称输入串的前半部分为 prefix。其实容易想到，其他的解还包括 (prefix + 1) 与 (prefix - 1) 所构造的回文串。通解除此之外没有别的了。但是有几个需要注意的地方，当一些数字附近的回文数有 11 或 9、101 或 99、1001 或 999 这样的数字时，需要单独将其求出，否则按照通常的写法可能会求出错误的数字。也可以直接对于每一个数字，都将离其最近的类似 101 或 99 的数字列为候选项，这样写起来容易一些。

####代码：

写法一（我真是上头了才会这么写）：
```
class Solution {
public:
    string nearestPalindromic(string n) {

		long long num = stoll(n), diff = LLONG_MAX, half_num;
        if (num > 9 && num < 12) {
            return "9";
        }
        if (num > 11 && num < 17) {
            return "11";
        }
        if (num > 16 && num < 20) {
            return "22";
        }
		string half_n, tmp_n, ans = n;
		int len = n.length();
		int half_len, flag;
		if (len % 2 == 1) {
			flag = 1;
			half_len = len / 2 + 1;
			half_n = n.substr(0, half_len);
		}
		else {
			flag = 0;
			half_len = len / 2;
			half_n = n.substr(0, half_len);
		}

		tmp_n = half_n;
		if (flag == 1) {
			for (int i = half_len - 2; i >= 0; --i) {
				tmp_n += half_n[i];
			}
		}
		else {
			for (int i = half_len - 1; i >= 0; --i) {
				tmp_n += half_n[i];
			}
		}
		if (abs(stoll(tmp_n) - num) < diff && (tmp_n != n)) {
			ans = tmp_n;
			diff = abs(stoll(tmp_n) - num);
		}

		string half_n2 = to_string(stoll(half_n) + 1);
		if (half_n2.length() > half_len) {
			if (flag == 1) {
				half_n2 = half_n2.substr(0, half_len);
				tmp_n = half_n2;
				for (int i = half_len - 1; i >= 0; --i) {
					tmp_n += half_n2[i];
				}
			}
			else {
				tmp_n = half_n2;
				for (int i = half_len + 1 - 2; i >= 0; --i) {
					tmp_n += half_n2[i];
				}
			}
		}
		else {
			tmp_n = half_n2;
			if (flag == 1) {
				for (int i = half_len - 2; i >= 0; --i) {
					tmp_n += half_n2[i];
				}
			}
			else {
				for (int i = half_len - 1; i >= 0; --i) {
					tmp_n += half_n2[i];
				}
			}
		}
		if (abs(stoll(tmp_n) - num) < diff && (tmp_n != n)) {
			ans = tmp_n;
			diff = abs(stoll(tmp_n) - num);
		}
		else if (abs(stoll(tmp_n) - num) == diff) {
			ans = stoll(ans) < stoll(tmp_n) ? ans : tmp_n;
		}

		half_n2 = to_string(stoll(half_n) - 1);
		if (half_n2.length() < half_len) {
			if (flag == 1) {
				tmp_n = half_n2;
				for (int i = half_len - 1 - 1; i >= 0; --i) {
					tmp_n += half_n2[i];
				}
			}
			else {
				half_n2 = half_n2 + half_n2.substr(half_len - 1 - 1, 1);
                tmp_n = half_n2;
				for (int i = half_len - 2; i >= 0; --i) {
					tmp_n += half_n2[i];
				}
			}
		}
		else {
			tmp_n = half_n2;
			if (flag == 1) {
				for (int i = half_len - 2; i >= 0; --i) {
					tmp_n += half_n2[i];
				}
			}
			else {
				for (int i = half_len - 1; i >= 0; --i) {
					tmp_n += half_n2[i];
				}
			}
		}
		if (abs(stoll(tmp_n) - num) < diff && (tmp_n != n)) {
			ans = tmp_n;
			diff = abs(stoll(tmp_n) - num);
		}
		else if (abs(stoll(tmp_n) - num) == diff) {
			ans = stoll(ans) < stoll(tmp_n) ? ans : tmp_n;
		}

		return ans;
    }
};
```

写法二（稍微正常一点）：

```
class Solution {
public:

	string nearestPalindromic(string n) {

		long long num = stoll(n), ans, diff = LLONG_MAX;
		int len = n.length();
		unordered_set<long long> candidates;
		candidates.insert(pow(10, len) + 1);
		candidates.insert(pow(10, (len - 1)) - 1);

		string half_n = n.substr(0, (len + 1) / 2);

		for (int i = -1; i <= 1; ++i) {
			string pre = to_string(stoll(half_n) + i);
			string tmp_n = pre + string(pre.rbegin() + (len & 1), pre.rend());
			candidates.insert(stoll(tmp_n));
		}

		candidates.erase(num);
		for (auto i : candidates) {
			if (abs(num - i) < diff) {
				diff = abs(num - i);
				ans = i;
			}
			else if (abs(num - i) == diff) {
				ans = min(ans, i);
			}
		}

		return to_string(ans);
	}

};
```

##LeetCode 956.最高的广告牌

####题目大意：
你正在安装一个广告牌，并希望它高度最大。这块广告牌有两个钢制支架，两边各一个。每个支架的高度必须相等。
这里有一堆可以焊接在一起的钢筋 rods。举个例子，如果钢筋的长度为 1、2 和 3，则可以将它们焊接在一起形成长度为6的支架。
返回广告牌的最大可能安装高度。如果没法安装广告牌，请返回 0。

示例1：
   **输入：** [1，2，3，6]
   **输出：** 6
   **解释：** 我们有两个不相交子集 {1，2，3} 和 {6}，它们拥有相同的 sum = 6。

示例2：
   **输入：** [1，2，3，4，5，6]
   **输出：** 6
   **解释：** 我们有两个不相交子集 {2，3，5} 和 {4，6}，它们拥有相同的 sum = 10。

示例3：
   **输入：** [1，2]
   **输出：** 0
   **解释：** 没法安装广告牌，所以返回 0。

注意：
   1. 0 <= rods.size() <= 20
   2. 1 <= rods[i] <= 1000
   3. 钢筋的长度总和最多为5000

####题解：
这道题钢筋长度总和也并不大，钢筋数目也不多，想到可以使用动态规划求解。求解时也主要着眼于状态的定义。令 dp[i][j] 为使用前 i 根钢筋，并且两个支架的高度差为 j 时，支架中较高的一侧的高度。那么当使用一根新的钢筋时，可以有三种选择：不使用它、放在原来就高的那一侧、放在原来低的那一侧。
   不使用它时，dp[i + 1][j] = MAX(dp[i + 1][j], dp[i][j]);
   放在原来高的那一侧时，dp[i + 1][j + rods[i]] = MAX(dp[i + 1][j + rods[i]], dp[i][j] + rods[i]);
   放在原来较矮的那一侧时，有可能短的一侧仍为短的一侧，也有可能短的一侧变为了长的一侧，故有 dp[i + 1][abs(j - rods[i])] = MAX(dp[i + 1][abs(j - rods[i])], dp[i][j], dp[i][j] - j + rods[i]);
初始化时所有 dp[i][j] 初始化为一个极小值，对于本题-5001就可以了。dp[0][0] 初始化为 0。最后只需按 dp[n][0] 的值输出即可。

####代码：

```
class Solution {
public:
    int tallestBillboard(vector<int>& rods) {

		int n = rods.size();
		int sum = 0;
		for (int i = 0; i < n; ++i) {
			sum += rods[i];
		}
		int dp[n + 1][sum + 1];
		for (int i = 0; i <= n; ++i) {
			for (int j = 0; j <= sum; ++j) {
				dp[i][j] = -5010;
			}
		}

		dp[0][0] = 0;
		for (int i = 0; i < n; ++i) {
			for (int j = 0; j <= sum; ++j) {
				//原来某些钢筋可以搭成的合法的高度差下：
				if (dp[i][j] >= 0) {
					//不使用这根钢筋时：
					dp[i + 1][j] = max(dp[i + 1][j], dp[i][j]);
					//放在原来长的一侧时：
					dp[i + 1][j + rods[i]] = max(dp[i + 1][j + rods[i]], dp[i][j] + rods[i]);
					//放在原来短的一侧时：
					if (j < rods[i]) {
						dp[i + 1][rods[i] - j] = max(dp[i + 1][rods[i] - j], dp[i][j] - j + rods[i]);
					}
					else {
						dp[i + 1][j - rods[i]] = max(dp[i + 1][j - rods[i]], dp[i][j]);
					}
				}
			}
		}

		return dp[n][0] < 0 ? 0 : dp[n][0];
	}
};
```



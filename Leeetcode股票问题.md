# Leetcode一系列股票问题

## 121 简单

给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。
如果你最多只允许完成一笔交易（即买入和卖出一支股票），设计一个算法来计算你所能获取的最大利润。
注意你不能在买入股票前卖出股票。

### 分析与题解

题目本质是要求数组的波谷和波峰的差值的最大值且前提是该波谷必须在波峰之前。或者说是对于某个位置i上的元素，求i的右边的最大值和i的差的最大值。

解法一：

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.empty()) return 0;
        int res=0,buy=prices[0];
        for(int i=1;i<prices.size();++i){
            // 当前位置的左边的波谷
            if(prices[i]<buy) buy=prices[i];
            // 当前位置的右边的波峰
            else res=max(res,prices[i]-buy);
        }
        return res;
    }
};
```

解法二：

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.empty()) return 0;
        int res=0,right=prices.back();
        for(int i=prices.size()-2;i>=0;--i){
            right=max(right,prices[i]);     // i 右边的最大值
            res=max(res,right-prices[i]);
        }
        return res;
    }
};
```

## 122 简单

给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。
设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。
注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

### 分析与题解

我们可以将数组表示成折线图，题目所求的就是折线图上所有|上升线段两端之差|的和。

解法：

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int res=0,profit=0;
        for(int i=0;i<prices.size();++i){
            if(i==0 || prices[i]<=prices[i-1]) res+=profit,profit=0;
            else profit+=prices[i]-prices[i-1];
        }
        return res+profit;
    }
};
```

## 123 困难

给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。
设计一个算法来计算你所能获取的最大利润。你最多可以完成 两笔 交易。
注意: 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

### 分析与题解

这题的难点在于限制了最多可以进行的交易次数。求解最优问题时比较容易想到采用动态规划的思路来解决问题，这就要求我们要确定求解问题需要知道的状态。

数组中的元素表示的是每一天的股票价格，所以我们的状态需要包括当前是哪一天；而根据题设要求，我们需要知道当前的第几次交易；此外每一天我们有两种状态，一种是持有股票一种是不持有。

因此我们共确定了三种状态：
dp[i][k][0]：第i天，已进行第k次交易，不持有股票
dp[i][k][1]：第i天，已进行第k次交易，持有股票

可以这样确定每天的状态：
dp[i][k][0]=max(dp[i-1][k][0],dp[i-1][k-1][0]+prices[i]);
表示我今天不持有股票，可以是我从昨天就不持有，也可以是我昨天持有但我今天卖了。
dp[i][k][1]=max(dp[i-1][k][1],dp[i-1][k][0]-prices[i]);
表示我今天持有股票，可以是我昨天就持有了，也可以是我今天刚买的。


```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n=prices.size();
        int dp[n+1][3][2];  // 天 交易次数 是否持有股票
        dp[0][0][0]=dp[0][1][0]=dp[0][2][0]=0;          // 第0天无交易
        dp[0][0][1]=dp[0][1][1]=dp[0][2][1]=INT_MIN;    // 第0天不可能持有股票
        for(int i=1;i<=n;++i) dp[i][0][0]=0,dp[i][0][1]=INT_MIN;
        for(int i=1;i<=n;++i){
            for(int k=1;k<=2;++k){
                dp[i][k][0]=max(dp[i-1][k][0],dp[i-1][k][1]+prices[i-1]);
                dp[i][k][1]=max(dp[i-1][k][1],dp[i-1][k-1][0]-prices[i-1]);
            }
        }
        return dp[n][2][0];
    }
};
```

## 188 困难

给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。
设计一个算法来计算你所能获取的最大利润。你最多可以完成 k 笔交易。
注意: 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

## 分析与题解

与上题的思路一样，不过要注意这题的k是否大于数组长度的一般，是的话则相当于可以进行无限次交易，那就转为122题的解法；否则采用123题的解法。

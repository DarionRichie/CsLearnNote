---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 714. Best Time to Buy and Sell Stock with Transaction Fee

> <https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/description/>

## Description


Your are given an array of integers `prices`, for which the `i`-th element is the price of a given stock on day `i`; and a non-negative integer `fee` representing a transaction fee.

You may complete as many transactions as you like, but you need to pay the transaction fee for each transaction. You may not buy more than 1 share of a stock at a time (ie. you must sell the stock share before you buy again.)

Return the maximum profit you can make.

**Example 1**:
**Input**: prices = [1, 3, 2, 8, 4, 9], fee = 2
**Output**: 8
**Explanation**: The maximum profit can be achieved by:
- Buying at prices[0] = 1
- Selling at prices[3] = 8
- Buying at prices[4] = 4
- Selling at prices[5] = 9

The total profit is ((8 - 1) - 2) + ((9 - 4) - 2) = 8.

**Note**:
- 0 < prices.length <= 50000.
- 0 < prices[i] < 50000.
- 0 <= fee < 50000.

## Solution

### explaination

At the end of the `i-th` day, we maintain `cash`, the maximum profit we could have if we did not have a share of stock, and `hold`, the maximum profit we could have if we owned a share of stock.

To transition from the `i`-th day to the `i+1`-th day, we either sell our stock `cash = max(cash, hold + prices[i] - fee)` or buy a stock `hold = max(hold, cash - prices[i])`. At the end, we want to return `cash`. We can transform `cash` first without using temporary variables because selling and buying on the same day can't be better than just continuing to hold the stock.

### code

```python
class Solution(object):
    def maxProfit(self, prices, fee):
        if len(prices)<1: return 0
        hold, cash = -prices[0], 0
        for price in prices[1:]:
            tp_cash = cash
            cash = max(cash, price-fee+hold)
            hold = max(tp_cash-price, hold)
        return cash
```

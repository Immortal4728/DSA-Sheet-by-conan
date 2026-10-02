# Pattern 07 — State Transition DP (State Machine DP)

State Transition DP (often called Finite State Machine DP) is used when the system transitions between a discrete set of states at each step (e.g., holding a stock, not holding a stock, being in a cooldown period). Instead of subproblems indexed solely by array position, each day/step maintains several variables representing maximum profit in each distinct physical or operational state.

---

## Core Concept & State Machine Diagram

```text
       ┌───────────── buy ──────────────┐
       │                                ▼
┌──────────────┐                 ┌──────────────┐
│ Not Holding  │                 │   Holding    │
│  (Unsold)    │                 │   (Bought)   │
└──────────────┘                 └──────────────┘
       ▲                                │
       │──────────── sell ──────────────┘
       │                (or sell + cooldown)
```

At step `i`, we calculate the maximum score/profit achievable while ending in state $S$ on day `i`.

### Decision Framework
1. **Identify States**: What mutually exclusive conditions can we be in at any time?
2. **Identify Transitions**: What actions can move us from State $A$ to State $B$?
3. **Formulate Equations**: `dp[day][state] = max(staying in state, coming from valid precursor state + gain/cost)`.
4. **Space Optimization**: Since state on day `i` depends only on day `i-1`, 2D tables contract to $O(1)$ space scalar variables.

---

## Pattern Questions (3 Canonical)

### 1. Best Time to Buy and Sell Stock with Cooldown — <a href="https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/" target="_blank">LeetCode 309</a>

#### Problem Statement
Given an array `prices` where `prices[i]` is the price of a stock on day `i`. You may complete as many transactions as you like, with the constraint that after selling a stock, you cannot buy stock on the next day (i.e., 1-day cooldown). Return max profit.

#### State Machine Definition
On day `i`, we can be in 1 of 3 states:
1. `hold`: Currently holding 1 share of stock.
2. `sold`: Just sold stock today (forces cooldown on day `i+1`).
3. `rest`: Not holding stock, and didn't sell today (eligible to buy today or stay in rest).

#### State Transitions
- `hold[i] = max(hold[i-1], rest[i-1] - prices[i])`
- `sold[i] = hold[i-1] + prices[i]`
- `rest[i] = max(rest[i-1], sold[i-1])`

#### Java Implementation
```java
public class StockWithCooldown {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length <= 1) return 0;

        int hold = -prices[0];
        int sold = 0;
        int rest = 0;

        for (int i = 1; i < prices.length; i++) {
            int prevHold = hold;
            int prevSold = sold;
            int prevRest = rest;

            // Buy stock today (must come from rest) or keep holding
            hold = Math.max(prevHold, prevRest - prices[i]);
            // Sell stock today
            sold = prevHold + prices[i];
            // Rest today (can come from previous rest or previous sold)
            rest = Math.max(prevRest, prevSold);
        }

        return Math.max(sold, rest);
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N)$ — Single linear scan.
- **Space Complexity:** $O(1)$ — Only 3 state variables updated in place.

---

### 2. Best Time to Buy and Sell Stock with Transaction Fee — <a href="https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/" target="_blank">LeetCode 714</a>

#### Problem Statement
Given an array `prices` and an integer `fee` representing a transaction fee paid upon selling (or buying) each stock. You may complete as many transactions as you like. Return max profit.

#### State Machine Definition
On day `i`, we maintain 2 states:
1. `hold`: Maximum profit on day `i` while holding stock.
2. `free`: Maximum profit on day `i` while NOT holding stock (cash only).

#### State Transitions
- `hold[i] = max(hold[i-1], free[i-1] - prices[i])`
- `free[i] = max(free[i-1], hold[i-1] + prices[i] - fee)` (fee deducted at sell time)

#### Java Implementation
```java
public class StockWithTransactionFee {
    public int maxProfit(int[] prices, int fee) {
        if (prices == null || prices.length == 0) return 0;

        int hold = -prices[0];
        int free = 0;

        for (int i = 1; i < prices.length; i++) {
            int prevHold = hold;
            int prevFree = free;

            hold = Math.max(prevHold, prevFree - prices[i]);
            free = Math.max(prevFree, prevHold + prices[i] - fee);
        }

        return free;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N)$ — 1 pass over price array.
- **Space Complexity:** $O(1)$ — 2 variables.

---

### 3. Best Time to Buy and Sell Stock III — <a href="https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/" target="_blank">LeetCode 123</a>

#### Problem Statement
Find the maximum profit you can achieve given an array `prices` where you may complete at most **two** transactions.

#### Multi-Transaction State Expansion
When the maximum number of transactions $K$ is small (e.g., $K = 2$), we unroll the state machine into $2K$ explicit variables:
1. `buy1`: Max profit after 1st buy.
2. `sell1`: Max profit after 1st sell.
3. `buy2`: Max profit after 2nd buy.
4. `sell2`: Max profit after 2nd sell.

#### State Transitions for Each Day `price`
- `buy1 = max(buy1, -price)`
- `sell1 = max(sell1, buy1 + price)`
- `buy2 = max(buy2, sell1 - price)`
- `sell2 = max(sell2, buy2 + price)`

Notice that updating sequentially in this exact order handles buying and selling on the same day cleanly without corrupting calculations!

#### Java Implementation
```java
public class StockIII {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length == 0) return 0;

        int buy1 = Integer.MIN_VALUE;
        int sell1 = 0;
        int buy2 = Integer.MIN_VALUE;
        int sell2 = 0;

        for (int price : prices) {
            buy1 = Math.max(buy1, -price);
            sell1 = Math.max(sell1, buy1 + price);
            buy2 = Math.max(buy2, sell1 - price);
            sell2 = Math.max(sell2, buy2 + price);
        }

        return sell2;
    }
}
```

#### Complexity Analysis
- **Time Complexity:** $O(N)$ — Linear pass over stock prices.
- **Space Complexity:** $O(1)$ — 4 scalar variables.

---

## Pattern Summary & State Machine Checklist

| Stock Problem Variant | Extra Constraint | Distinct States | Key Transition Vector |
| :--- | :--- | :--- | :--- |
| **LC 309 (Cooldown)** | 1-day lock post sell | 3 (`hold`, `sold`, `rest`) | `rest` $\to$ `hold`, `hold` $\to$ `sold`, `sold` $\to$ `rest` |
| **LC 714 (Fee)** | Pay `fee` per sell | 2 (`hold`, `free`) | Pay fee when computing `free` from `hold` |
| **LC 123 (At Most 2 Txns)**| Maximum 2 trades total | 4 (`buy1`, `sell1`, `buy2`, `sell2`)| Cascading unrolled pipeline |

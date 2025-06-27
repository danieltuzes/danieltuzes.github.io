---
title: interesting problems
---

- [Round table serving problem](#round-table-serving-problem)
- [Python coding challenges](#python-coding-challenges)
  - [Coin exchange I](#coin-exchange-i)
  - [Coin exchange II](#coin-exchange-ii)
  - [Twitter](#twitter)

## Round table serving problem

**Problem Statement**: Consider a round table with people sitting at fixed positions. Food is distributed starting from a marked position, the "head" of the table. At each step, the person currently serving chooses to give the food to either the person on their immediate left or right, with equal probability. This process continues: any person who receives the food then chooses left or right with equal probability, and so on. A person may be served multiple times during this process.

Your task is to determine **at which position to sit** in order to **maximize the probability of being served last**.

<details><summary>Solution</summary>

**Notation and Observations**: Let us fix a position at the round table and define the following events:

- **$L$**: the person to the **left** of us is served **before** we are.  
- **$R$**: the person to the **right** of us is served **before** we are.  
- **$\mathcal{L}$**: the **left side** of the table is served **before** the right side.  
- **$\mathcal{R}$**: the **right side** of the table is served **before** the left side.

Note that:

- Eventually, one side must be served before us:

  $$
  \mathbb{P}(L \cup R) = 1
  $$

- $\mathcal{L}$ and $\mathcal{R}$ are disjoint and cover the whole probability space:

  $$
  \mathcal{L} \cap \mathcal{R} = \varnothing, \quad \mathcal{L} \cup \mathcal{R} = \Omega
  $$
  $$
  \Rightarrow \mathbb{P}(\mathcal{L}) + \mathbb{P}(\mathcal{R}) = 1
  $$

- If a side is served before the other side, then the person on that side is served before us:

  $$
  \mathcal{L} \subseteq L, \quad \mathcal{R} \subseteq R
  $$

We want to compute the probability that we are served **last** — that is, both neighbors are served before us:

$$\mathcal{P} := \mathbb{P}(L \cap R)$$

If we plot a graph, we could see that $L \cap R = (L \cap \mathcal{R}) \cup (R \cap \mathcal{L})$ but let's prove it rigorously.
We decompose both $L$ and $R$ using the partition $\mathcal{L} \cup \mathcal{R} = \Omega$:

$$\begin{aligned}
L &= (L \cap \mathcal{L}) \cup (L \cap \mathcal{R}) \\\\
R &= (R \cap \mathcal{L}) \cup (R \cap \mathcal{R})
\end{aligned}$$

Then:

$$
\begin{aligned}
L \cap R
&= \left[(L \cap \mathcal{R}) \cup (L \cap \mathcal{L})\right]
   \cap \left[(R \cap \mathcal{R}) \cup (R \cap \mathcal{L})\right] \\\\
&= \left[ (L \cap \mathcal{R}) \cup \mathcal{L} \right]
   \cap \left[ \mathcal{R} \cup (R \cap \mathcal{L}) \right] \\\\
&= \underbrace{(L \cap \mathcal{R} \cap \mathcal{R})}\_{=L \cap \mathcal{R}}
   \cup \underbrace{(L \cap \mathcal{R} \cap R \cap \mathcal{L})}\_{=\varnothing}
   \cup \underbrace{(\mathcal{L} \cap \mathcal{R})}\_{=\varnothing}
   \cup \underbrace{(\mathcal{L} \cap R \cap \mathcal{L})}\_{=R \cap \mathcal{L}} \\\\
&= (L \cap \mathcal{R}) \cup (R \cap \mathcal{L})
\end{aligned}
$$

$\mathcal{L}$ and $\mathcal{R}$ are disjoint events, so:
$$
\mathbb{P}(L \cap R) = \mathbb{P}(L \cap \mathcal{R}) + \mathbb{P}(R \cap \mathcal{L})
$$

Now apply the **definition of conditional probability** (if $\mathbb{P}(\mathcal{R}) > 0$ and $\mathbb{P}(\mathcal{L}) > 0$):
$$
\mathbb{P}(L \cap \mathcal{R}) = \mathbb{P}(L \mid \mathcal{R}) \cdot \mathbb{P}(\mathcal{R})
$$
$$
\mathbb{P}(R \cap \mathcal{L}) = \mathbb{P}(R \mid \mathcal{L}) \cdot \mathbb{P}(\mathcal{L})
$$

Note that for symmetry, $\mathbb{P}(L \mid \mathcal{R}) = \mathbb{P}(R \mid \mathcal{L})$, so we can write:
$$
p := \mathbb{P}(L \mid \mathcal{R}) = \mathbb{P}(R \mid \mathcal{L})
$$

Then:
$$
\mathbb{P}(L \cap R) = p \cdot \left( \mathbb{P}(\mathcal{R}) + \mathbb{P}(\mathcal{L}) \right) = p
$$
Note how $p$ does not depend on the position we choose at the round table. If the first person served sit on our right, then $\mathbb{P}(R)  = \mathbb{P}(\mathcal{R}) = 1$, and with $p := \mathbb{P}(L \mid \mathcal{R}) = \mathbb{P}(L)$ the equation $\mathbb{P}(L \cap R) = p$ still holds. So we can conclude that the probability of being served last is independent of the position we choose at the round table.

</details>

## Python coding challenges

### [Coin exchange I](https://leetcode.com/problems/coin-change/)

<details>
<summary>Source code</summary>

```python
from typing import List
from functools import lru_cache

class Solution:
    def __init__(self):
        self.memo = {}

    def coinChange2(self, coins: List[int], amount: int) -> int:
         # bottom up
         # coins   1, 2, 7
         # 1    2    3     4       7    8    9  ...  40
         # 1    1    2     2                                  # not this
         # [1]  [2]  [1,2] [2,2]   []                         # but this
        if amount == 0:
            return 0
        coins = sorted(coins)
        largest = coins[-1]

        exchngs = [[] for i in range(amount+1)]  # at i stores the smallest list of coins summing up to i
        for i in range(1,amount+1):
           for coin in coins:
                last_amount = i-coin
                if last_amount < 0:
                   continue
                if last_amount == 0:
                   exchngs[i] = [coin]
                elif len(exchngs[last_amount]) == 0:
                   continue
                if len(exchngs[i]) == 0:
                   exchngs[i] = exchngs[last_amount] + [coin]
                elif len(exchngs[i]) > len(exchngs[last_amount]) + 1:
                   exchngs[i] = exchngs[last_amount] + [coin]

        length = len(exchngs[amount])
        if length == 0:
            return -1

        return length

    def coinChange(self, coins: List[int], amount: int) -> int:
        # top down
        if amount == 0:
            return 0
        res = self.coinChgLst(coins, amount)
        return len(res) if res is not None else -1

    def coinChgLst(self, coins: List[int], amount: int) -> List[int]:
        if amount in self.memo:
            return self.memo[amount]

        best = None  # shortest list of coins summing up to amount
        for coin in coins:

            rem = amount-coin
            if rem == 0:
                self.memo[amount] = [coin]
                return [coin]
            if rem < 0:
                continue
            cand = self.coinChgLst(coins, rem)
            if cand is None:
                continue  # no solution for this amount
            if best is None or len(best) > len(cand):
                best = cand + [coin]

        self.memo[amount] = best
        return best
```

</details>

### [Coin exchange II](https://leetcode.com/problems/coin-change-ii/)

<details>
<summary>Souce code</summary>

```python
class Solution:
    def change(self, amount: int, coins: List[int]) -> int:
        if amount == 0:
            return 1
        coins = sorted(coins, reverse=True)
        exchngs = [0 for i in range(amount+1)]  # at i stores the number of ways to exchange i amount

        for coin in coins:
            for i in range(1,amount+1):
                last_amount = i-coin
                if last_amount < 0:
                   continue
                if last_amount == 0:
                   exchngs[i] += 1
                elif exchngs[last_amount] == 0:
                   continue

                exchngs[i] += exchngs[last_amount]

        return exchngs[amount]
```

</details>

### [Twitter](https://leetcode.com/problems/design-twitter/)

<details>
<summary>Source code</summary>

```python
class Twitter:

    def __init__(self):
        self.tweetsByUsers = defaultdict(deque)  # userID, tweetID
        self.subscripts = defaultdict(set)  # who they are subscribed to
        self.timer = 0
        self.increment = -1  # choose 1 or -1 depending on whether you want to use be memory efficient or time efficient

    def postTweet(self, userId: int, tweetId: int) -> None:
        self.tweetsByUsers[userId].appendleft(tuple((self.timer, tweetId)))
        self.timer += self.increment
        if len(self.tweetsByUsers[userId]) > 10:
            self.tweetsByUsers[userId].pop()

    def getNewsFeed(self, userId: int) -> List[int]:
        if self.increment == 1:
            ret = []  # heapq with maxsize 10, (time, tweetId)
            users = list(self.subscripts[userId]) + [userId]
            for user in users:
                tweets = self.tweetsByUsers[user]
                for tweet in tweets:
                    if len(ret) >= 10:
                        heapq.heappushpop(ret, tweet)
                    else:
                        heapq.heappush(ret, tweet)
            ret = sorted(ret, reverse=True)
            ret = [val[1] for val in ret]
            return ret

        else:
            ret = []  # list with maxsize 10, (time, tweetId)
            users = list(self.subscripts[userId]) + [userId]
            potentialTweets = defaultdict(deque)  # copy of tweetsByUsers, but only for the users we are interested in
            nominees = []  # heapq with the next most recent tweet from each of the users, storing (time, tweetId, userId)
            for user in users:
                potentialTweets[user] = self.tweetsByUsers[user].copy()
                if potentialTweets[user]:
                    heapq.heappush(nominees, potentialTweets[user].popleft() + (user,))

            while nominees:
                _, tweetId, user = heapq.heappop(nominees)
                ret.append(tweetId)
                if potentialTweets[user]:
                    heapq.heappush(nominees, potentialTweets[user].popleft() + (user,))
                if len(ret) == 10:
                    break

            return ret

    def follow(self, followerId: int, followeeId: int) -> None:
        self.subscripts[followerId].add(followeeId)

    def unfollow(self, followerId: int, followeeId: int) -> None:
        self.subscripts[followerId].discard(followeeId)
```

![statistics for method `-1`](python/image.png)

</details>

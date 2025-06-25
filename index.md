---
title: Home
---

## Daniel Tuzes's personal site

- [Go to Page 2](page2.md)

Local:

- [Daniel Tuzes's personal site](#daniel-tuzess-personal-site)
  - [python](#python)

### python

<details>
<summary>Coin exchange I</summary>

[leetcode](https://leetcode.com/problems/coin-change/)

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

<details>
<summary>Coin exchange II</summary>

[leetcode](https://leetcode.com/problems/coin-change-ii/)

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

<details>
<summary>Twitter</summary>

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

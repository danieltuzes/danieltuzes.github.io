---
title: Home
---

## Daniel Tuzes's personal site

- [Go to Page 2](page2.md)

Local:

- [Daniel Tuzes's personal site](#daniel-tuzess-personal-site)
  - [python](#python)

### python

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
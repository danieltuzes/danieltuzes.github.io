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


    def postTweet(self, userId: int, tweetId: int) -> None:
        self.tweetsByUsers[userId].appendleft(tuple((self.timer, tweetId)))
        self.timer += 1
        if len(self.tweetsByUsers[userId]) > 10:
            self.tweetsByUsers[userId].pop()


    def getNewsFeed(self, userId: int) -> List[int]:
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


    def follow(self, followerId: int, followeeId: int) -> None:
        self.subscripts[followerId].add(followeeId)
        

    def unfollow(self, followerId: int, followeeId: int) -> None:
        self.subscripts[followerId].discard(followeeId)
```

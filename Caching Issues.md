
# Stale Sets

Stale set situation happens when outdated data is set in the cache when compared to the source-of-truth database.

This scenario can happen quite easily while using the [[Database Caching Strategies#Cache Aside Strategy]] strategy where the incoming request checks for data in cache and if it doesn’t find the data, it fetches from the database and inserts the same in the cache.

**But why does Stale sets happen?**

See the below sequence diagram that shows the scenario:

![[Pasted image 20240308114425.png]]

The steps leading to this are as follows (from the perspective of a single key):

- User A tries to read from cache but gets a cache miss.
    
- The query goes to the database which returns the data.
    
- Meanwhile, User B updates the database successfully. It also deletes the record (invalidates) from the cache.
    
- Now, User A writes to the cache because there was a cache miss earlier. But it writes the data it had read earlier which is now stale after User B was successful.
    
- At the end User C comes along and reads stale data from the cache.


# Thundering Herd

The thundering herd problem occurs in a highly concurrent environment with many users.

When many users make a request for a particular record simultaneously and there’s a cache miss, the thundering herd problem gets triggered. All of these requests will move on to read the data from source database.

In other words, despite the use of caching, the database is hit by a thundering herd of users.

See the below diagram that shows the concept of Thundering Herd.

![[Pasted image 20240308115217.png]]

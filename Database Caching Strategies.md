
Databases are critical to an application’s performance.

In fact, I think they are on top of the most important factors when it comes to performance.

The difference between good performance and poor performance comes down to how well you deal with the **typical database challenges** such as:

- Query processing speed
    
- Cost of scaling
    
- Making data access easy

# Cache Aside Strategy

![[Pasted image 20240308115550.png]]

This strategy is great for **read-heavy workloads**.

Also, since the cache is separate from the database, a cache failure cannot cripple the system. The application can always go directly to the database.

That’s wonderful for **resiliency**.

However, the main drawback of cache-aside is the potential inconsistency between the cache and the database. Any data being written or updated goes to the database and therefore, the cache may have a period of inconsistency with the primary database.

# Read Through Strategy

![[Pasted image 20240308115830.png]]

In cache-aside, the application is responsible for fetching the data and populating the cache. In read-through, all of this is done by a library or a cache provider.

The disadvantage of read-through is also the same as cache-aside:

- potential data inconsistency between the cache and the database
    
- the need to go to the database every time a brand-new read request comes through.

# Write Through Strategy

The write-through strategy tries to get around the problem with read-through.

Instead of writing data to the database, the application first writes to the cache and the cache immediately writes to the database.

![[Pasted image 20240308120058.png]]

The advantage of write-through is that the cache is ensured to have any written data. New read requests won’t experience a delay while the cache requests the data from the main database.

**What’s the downside?**

Extra write latency because the data must go to the cache and then to the database. While the impact should be negligible, there are still two writes happening one after the other.


# Write-Back Strategy

**Write-back strategy** is essentially a variation of the **write-through** strategy.

There’s just one key difference.

In the write-back strategy, the application writes directly to the cache (just like write-through). However, the cache **does not immediately** write to the database but after a delay.

The advantage of this is that the strain on the cache is reduced in case you have a write-heavy workload. Requests to the database are batched and the overall write performance is improved.

The downside is that in case of a cache failure, there are chances of possible data loss.


# Write Around Strategy

![[Pasted image 20240308120345.png]]

This is basically the same as a cache-aside or read-through strategy with the added context around how the writes are handled.


# Summary

![[Pasted image 20240308120411.png]]



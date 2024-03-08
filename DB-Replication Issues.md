# Vanishing Updates

The following is the explanation of how a DB replication lag occurs:

![[Pasted image 20240307120654.png]]


- User A updates the profile on the primary or leader node.
    
- The immediate request to read the contents of the profile goes to a replica that has not yet been updated. The user sees stale data and will be unhappy.
    
- Eventually, the async replication does its job and the replication is successful.

**So - how can systems get around this issue?**

By providing **read-after-write** consistency also known as **read-your-writes** consistency.

Basically, it’s a guarantee that if a user makes some updates, they will always see those updates when they reload the page.

This reassures the user that their own input has been saved correctly.

However, this approach doesn’t make any consistency guarantees for other users.

**How do you implement this guarantee?**

There are several ways to implement **read-after-write** guarantee:

- When reading something that the user has modified, read it from the leader. Otherwise, read it from the follower. This is ideal for cases such as profile information that can only be editable by the owner of the profile.
    
- The client keeps track of the timestamp for the most recent write. The system ensures that the replica serving any reads for that user reflects updates until that timestamp.
    
- Monitor the replication lag on followers and prevent queries on any followers or replicas that are more than one or two minutes behind the leader.
    
- Databases (such as DynamoDB) and Object Storage (such as S3) have started providing **consistency guarantees** as an option.


# Going Backwards in Time

It can be explained by the following diagram:

![[Pasted image 20240307123443.png]]

- User A adds a comment on a blog post. The write operation is successful on the **Primary** node.
    
- The comment is successfully replicated to **Replica 1**.
    
- User B checks the comments on the blog post.
    
- The request goes to Replica 1 and User B is able to read the comment made by User A in Step 1
    
- User B refreshes the page for whatever reason but this time, the read request goes to **Replica 2 which is lagging**.
    
- There is no sign of the comment made by User A.
    
- After some time, **Replica 2** also receives the updates from the **Primary** but the user experience is ruined.

So - how to get around this issue?

Your system needs to guarantee **Monotonic Reads**.

This guarantee means that if one user makes several reads in sequence, they won’t see time go backward.

**In other words, they won’t read older data after previously reading newer data.**

How do systems manage this?

A simple trick is to make sure that each user always makes their reads from the same replica.

This stickiness can be achieved by choosing the replica based on the **hash of the User ID** rather than randomly.

# Violation of Causality

This is a special situation that happens in the case of partitioned or sharded databases.

The below illustration shows this problem:

![[Pasted image 20240307123950.png]]

- User A writes a message _“Hi UserB, How Are You?”_ to the Leader node of Partition 1.
    
- User B replies _“I’m fine, User A”_ to the Leader node of Partition 2.
    
- The message _“I’m fine, User A”_ gets replicated to the Follower node of Partition 2
    
- The message _“Hi UserB, How Are You?”_ gets replicated to the Follower node of Partition 1 but it happens much later than the replication for Partition 2.
    
- Now, imagine there’s a user called Viewer who is reading these messages from the follower nodes.
    
- The Viewer reads _“I’m fine User A”_ from Partition 2 (follower). Then, she reads _“Hi UserB, How Are You?”_ from Partition 1 (follower).

To prevent this situation, the system needs to provide another guarantee known as **consistent prefix reads**.


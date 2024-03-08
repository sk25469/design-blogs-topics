
**Consistent Hashing is a technique used for distributing keys uniformly across a cluster of nodes.**

The focus behind Consistent Hashing is to minimize the number of keys that need to be moved around when we add or remove a node from the cluster.


# Why Consistent Hashing?

Normally in an architecture where there are fixed no. of servers, let's say `N` . 
For every key, we can hash it and assign it to a particular server. We can find it by using this formula - `server_index = hash(key) % N` , where `N = no. of servers`

This ensures the keys are distributed evenly given the hash functions distributed the hashes evenly.
But what if the no. of servers change dynamically, for different loads. If `N` changes, the keys have to be distributed again because the server index changes and depends on `N`.

So, **Consistent Hashing** is the way where we can make sure that we need to re-distribute a fraction of keys when no. servers change.


Below are the steps to demonstrate how Consistent Hashing works:

## STEP 1

The keys are hashed using a hash function.

The output range of these key values is treated as a fixed circular space or ring. For example, in the below diagram K1, K2, K3 and K15 are the positions of the keys on the hash ring.

![[Pasted image 20240308112504.png]]

## STEP 2

Next, the servers or nodes are also hashed using the IP address or the domain name as input.

We use the same hashing function to determine their respective positions on the ring.

See the below diagram:

![[Pasted image 20240308112528.png]]

## STEP 3

Lastly, for every key, we traverse the ring in a clockwise direction starting from the position of the key.

Once a node is found, we store the key on that node.

See the below diagram:

![[Pasted image 20240308112552.png]]

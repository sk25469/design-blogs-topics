## Register

* Stores data in Amazon DynamoDB 
* Sends it to message queue for storing the data in a db shard according to the location

## Searching Potential Matches

* Can't directly search nearby users based on latitude and longitude at scale
* Can't divide the world into evenly spaced grids to avoid **hot shard problem** (A **hot shard** is an excessive load on a single partition)
* Uses S2 library (**S2** is a square-shaped hierarchical geospatial indexing system)
* S2 divides the world into square cells, giving each cell a unique 64 bit identifier
* Nearby users are stored in same db shard, reducing query
* S2 returns the db shards based on latitude and longitude
	* Query is done on all the shards parallely to get nearby users

## Matching Users

* Swipes are sent to data stream like Amazon Kinesis
* Likes are sent to cache
* Match workers are running to check data from streams to match from the likes cache
* Once users match, they send the notification in real time using Web Sockets
* Disliked profile are stored in S3, to improve user recommendation

## Other Important Points

* They calculate the unique number of users to find the load on a shard. And users within a shard are usually in the same time zone
* Also most data operations at Tinder are read operations. So they use Redis cache for scale.
* They use the cache-aside pattern, i.e., they check the cache for data. And if it’s absent, they fall back to the database. Also the cache gets updated with the data.
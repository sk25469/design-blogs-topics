
# Level 4

As the name suggests, Layer 4 load balancers operate in the transport layer of the **OSI model**.

Yes, the famous OSI model from Computer Networks!

**What does it mean?**

It means that the load balancer makes routing decisions solely on the basis of information available at Layer 4.

Information such as IP address or port.

In other words, a **Layer 4 load balancer** cannot see the message in the data packet.

Here’s what it looks like:

**Advantages of Layer 4 load balancers:**

- Simpler to run and maintain
    
- Better performance since no data lookup
    
- More secure since there’s no need to decrypt the TLS data
    
- Only one TCP connection
    

**Disadvantages:**

- No smart load balancing
    
- No routing to different service types
    
- No caching since the LB can’t even see the data or the message.

![[Pasted image 20240212143312.png]]



# Level 7

Layer 7 load balancers operate at layer 7 of the OSI model.

That was predictable, I suppose.

Basically, this means they deal with layer 7 protocols such as HTTP(S), WebSocket, FTP, and SMTP.

**But what does that change?**

The Layer 7 load balancers can see the **actual data** within the packet and make routing decisions based on that data.

Here’s how Layer 7 load balancers work:

- Let’s say you have two microservices - one dedicated to the blog posts (`/blog`) and the other dedicated to the comments (`/comments`).
    
- The client makes a request to the load balancer by establishing a TCP connection, saying that it wants to access the `/blog` route.
    
- The load balancer **decrypts** the data and checks the configured rules.
    
- Based on the request destination, the load balancer establishes a new TCP connection to the server instances hosting the Blog microservice.
    
- Once the response comes back, it sends over the response to the client.

**Advantages:**

- Smarter load balancing (**big benefit**)
    
- LB can also cache data
    
- Can play the role of a reverse proxy
    

**Disadvantages**:

- Expensive to run and maintain
    
- Needs to decrypt the data
    
- Maintains 2 TCP connections - one from the client to the load balancer and another from the load balancer to the service

![[Pasted image 20240212170023.png]]


eg. We used **Kong**, it's a **Level 7** LB.


# Load Balancing Algorithms

It all depends on the load balancing algorithm being used.

Broadly, you can divide these algorithms into two categories:

- Static
    
- Dynamic

### Static Load Balancing Algorithms

The static algorithms distribute traffic among servers based on **pre-determined** rules or fixed configuration.

To put things simply, these algorithms don’t adapt to changing server workloads.

A few popular static load balancing algorithms are as follows:

#### 1 - Round Robin

Requests are distributed sequentially across a group of servers.

The main assumption is that the service is stateless because there’s no guarantee that subsequent requests from the same user will reach the same instance.

#### 2 - Sticky Round Robin

This is a better alternative to round-robin since subsequent requests from the same user go to the same instance.

Depending on the use case, this can be a desirable quality for load balancing.

#### 3 - Weighted Round Robin

In this algorithm, each server instance gets a specific **weight value**.

This value determines the proportion of traffic that will be directed to the particular server.

Servers with higher weights receive a larger share of the traffic while servers with lower weights receive a smaller share.

For example, if server instance A has a weight of 0.75 and instance B has a weight of 0.25, server A will receive thrice as much traffic as instance B.

That’s a super-useful approach when different servers have different capacity levels and you want to assign traffic based on capacity.


![[https___substack-post-media.s3.amazonaws.com_public_images_6e36f637-5875-41e7-af5b-37b588af47c1_1080x1080.gif]]

### Dynamic Load Balancing Algorithms

Dynamic load balancing depends on some property that keeps on changing to make routing decisions.

Let’s look at a couple of such algorithms.

#### 1 - Least Connections

In this algorithm, a new request is sent to the server instance with the least number of connections.

Of course, the number of connections is determined based on the relative computing capacity of a particular server.

So, server instances with more resources can support more connections as compared to instances with low resources.

#### **2 - Least Response Time**

In this case, the load balancer assigns incoming requests to the server with the lowest response time in order to minimize the overall response time of the system.

This is great for cases where response time is critical and you want to ensure that the request goes to an instance that can provide a quick response.



![[https___substack-post-media.s3.amazonaws.com_public_images_d47b527b-49e8-4f75-ba6c-fedccf4fc581_1080x1080.gif]]


## What if the Load Balancer goes down?

This is a common sentiment I’ve seen come up during design discussions.

Generally, the question is hand-waived by saying that the cloud provider will take care of it.

And that’s mostly true.

Modern cloud systems have reached a point where developers need not concern themselves with the ins and outs of maintaining infrastructure pieces like the load balancer.

These infra pieces are treated as services and it’s the job of the service provider to make sure that things are working fine.

But it can be a good exercise to consider what a high availability load balancer setup looks like.

Here’s a diagram that attempts to show the big picture:

![[Pasted image 20240212172004.png]]

Ultimately, when you talk about a high-availability load balancing setup, what you mean is that the load balancer should not become a **single point of failure (SPOF)**.

**And how do we remove a single point of failure?**

Of course, by investing in **redundancy**.

In the above example, we have multiple load balancers (one active and one or more passive) behind a static IP address. This static IP address can be remapped from one server to another.

When a user accesses your website, the request goes through the floating IP address to the active load balancer. 

If that load balancer fails for some reason, the failover mechanism will detect it and automatically reassign the IP address to one of the passive servers that will take over the load balancing duties.
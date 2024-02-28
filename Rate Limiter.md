## **🖥 How Rate Limiting Works?**

Rate limiting is the concept of controlling the amount of traffic being sent to a resource.

**How can you achieve this control?**

By using a **rate limiter** – a component that lets you control the rate of network traffic to an API server or any other application that you want to protect.

Here’s a big-picture view of how a rate limiter sits in the context of a system.

### Key Concepts of Rate Limiting

There are 3 key concepts shared by most rate-limiting setups.

Here’s a quick look at each of them:

![[Pasted image 20240212172357.png]]

- **Limit** — Defines the maximum number of requests allowed by the system in a given time span. For example, Twitter (now X) recently rate-limited unverified users to only view 600 tweets per day.
    
- **Window** — The time period for the limit. It can be anything from seconds to minutes to even days.
    
- **Identifier** — A unique attribute that lets you distinguish between individual request owners. For example, things like a User ID or IP address can play the role of an identifier.

### Designing a Rate Limiter

The basic premise of a rate limiter is quite simple.

On a high level, you count the number of requests sent by a particular user, an IP address or even a geographic location. If the count exceeds the allowable limit, you disallow the request.

However, there are several considerations to be made while **designing a rate limiter**.

For example:

- Where should you store the counters?
    
- What about the rules of rate limiting?
    
- How to respond to disallowed requests?
    
- How to ensure that changes in rules are applied?
    
- How to make sure that rate limiting doesn’t degrade the overall performance of the application?
    
- To balance all of these considerations, you need several pieces that work in combination with each other.

Here’s an illustration that depicts such a system.

![[Pasted image 20240212173040.png]]

Let’s understand what’s going on over here:

- When a request comes to the API server, it first goes to the rate limiter component. The rate limiter checks the rules in the rules engine.
    
- The rate limiter proceeds to check the rate-limiting data stored in the cache. This data basically tells how many requests have already been served for a particular user or IP address. The reason for using a cache is to achieve high throughput and low latency.
    
-  If the request falls within the acceptable threshold, the rate limiter allows it to go to the API server.
    
- If the request exceeds the limit, the rate limiter disallows the request and informs the client or the user that they have been rate-limited. A common way is to return **HTTP status code 429 (too many requests)**.
    

There are a couple of improvements you can have over here:

- **First**, instead of returning HTTP status code 429, you can also simply drop a request silently. This is a useful trick to fool an attacker into thinking that the request has been accepted even when the rate limiter has actually dropped the request completely.
    
- **Second**, you could also have a cache in front of the rules engine to increase the performance. In case of updates to the rules, you could have a background worker process updating the cache with the latest set of rules.
### How does a URL Shortener work ? 
At high level, following operations are executed:
- server generates a unique short URL for each long URL
- server encodes the short URL for readability
- server persists the short URL in the data store
- server redirects the client to the original long URL against the short URL
---
### Terminology

- **[[Microservices]]**
- **Service Discovery:** process of automatically detecting devices and services on the network
- **CDNs**
- **API**
- **Encoding**
- **Encryption**
- **Hashing**
- **Bloom filter:** a memory efficient data structure to check whether an element is present in a set
---
### Reasons to shorten a URL
- track clicks for analytics
- beautify a URL
- disguise the underlying URL for affiliates
- some instant messaging services limit the count of characters on the URL
---
### Questions to ask to the interviewer
1. What are the use cases of the system ?
	URL Shortening and redirection to the original long URL
2. What is the amount of DAU for writes ?
	100 Million DAU
3. How many years should we persist the short URL by default ?
	5 years
4. What is the anticipated read:write ratio of the system ?
	100:1
5. What is the usage pattern of shortened URL ?
	Most of the shortened URLs will be accessed only once after the creation
6. Who will use the service ?
	General public
7. What is the reasonable length of a short URL ?
	at most 9 characters
---
### Requirements
#### Functional Requirements
- service similar to Tiny URL or bitly
- A **client (user)** enterse a long URL into the system and the system returns a shortened URL
- the client visiting the short URL must be redirected to the original URL
- Multiple users entering the same long URL must receive the same short URL (1-to-1 mapping)
- The short URL should be readable
- The short URL should be collision free
- The short URL should be non-predictable
- client should be able to choose a custom short URL
- short URL should be web-crawler friendly (SEO)
- should support analytics (not real time), such as the number of redirections from the shortened URL (if this is opted in for, then pre-exisiting short URL won't be used)
- client optionally defines the expiry time of the short URL
#### Non Functional Requirements
- High availability
- low latency
- high scalability
- durable
- fault tolerant
#### Out of Scope
- user registers an account
- user sets the visibility of the short URL
---

### Database
This system is read heavy (100:1 read-write ratio). Hence, the dominant usage pattern is the redirection from short URLs to long URLs 
#### DB Schema
Major entities of DB are the Users table and URL table. The relationship between these will be **1-to-many**. A user might generate multiple short URLs but a short URL is generated only by a single user.
![[Pasted image 20241202213814.png]]
#### Type of data store
A NoSQL data store such as MongoDB is used to store the URL table. Reasons for choosing NoSQL:
- Non-relational data
- Dynamic or flexible schema
- No need for complex joins
- Store many TB of data
- Very data intensive workload
- Very high throughput for IOPS
- Easy horizontal scaling

A SQL database such as Postgres or MySQL is used to store the Users table. Reasons for choosing SQL:
- Strict Schema
- Relational Data
- Need for complex joins
- Lookups by index are pretty fast
- ACID compliance

So, when the client requests to identify the owner of a URL, server queries the Users table and the URL table from different data stores and joins the tables at the application level.

--- 
### Capacity Planning
Number of registered users is relatively limited compared to the number of short URLs generated. The [[Back of the Envelope Calculations (Capacity Planning)]] calculations focus on the URL data.
However, the calculated numbers are approximations.

---
### High Level Design
#### Encoding
Reasons to encode a shortened URL:
- improve the readability of the short URL
- make short URLs less error-prone
The encoding format to be used must yield a deterministic output. The potential data encoding formats that satisfy the URL shortening use cases are:
![[Pasted image 20241203211508.png]]
The _base58_ encoding format is similar to base62 encoding except that _base58_ avoids non-distinguishable characters such as O (uppercase O), 0 (zero), and I (capital I), l (lowercase L). The characters in _base62_ encoding consume 6 bits (2⁶ = 64). A short URL of 7 characters in length in _base62_ encoding consumes [42 bits](https://www.perplexity.ai/search/the-base58-encoding-format-is-ZfKuEzVYQ9e_than_ixEIg).
Formula for counting the total number of short URLs that are produced using a specific encoding format and the number of characters in the output:
```
Total count of short URLs = branching factor ^ depth
where the branching factor is the base of the encoding format and depth is the length of characters
```
![[Pasted image 20241203212301.png]]
A total count of 3.5 trillion short URLs is exhausted in 100 years when 1000 short URLs are used per second. The guidelines on the encoded output format to improve the readability of a short URL are the following:
- the encoded output contains only alphanumeric characters
- the length of the short URL must not exceed 9 characters
The **time complexity** of base conversion is O(k), where k is the number of characters (k = 7). The time complexity of base conversion is reduced to constant time O(1) because the number of characters is fixed
#### Write Path
Everything working on a single machine doesn't meet the scalability requirement, so we can move out the key generation function to a dedicated Key Generation Service (KGS) to scale out the system
![[Pasted image 20241203213608.png]]
Different solutions to shorten a URL are:
- Random ID Generator
- Hashing function
- Token Range
##### Random ID Generator Solution
The Key Generation Service (**KGS**) queries the random identifier (**ID**) generation service to shorten a URL. The service generates random IDs using a random function or Universally Unique Identifiers (UUID). Multiple instances of the random ID generation service must be provisioned to meet the demand for scalability.
![[Pasted image 20241203214741.png]]
This solution has the following tradeoffs:
- probability of collisions is high due to randomness
- breaks the 1-to-1 mapping between a short URL and a long URL
- coordination between servers is required to prevent collision
- frequent verification of the existence of a short URL in the DB is a bottleneck

##### Hashing Function Solution
KGS queries the hashing function service to shorten a URL. Hashing function service accepts a long URL as an input and executes a hash function such as the message digest (MD5) algorithm to generate a short URL.
Architecture will be same as random ID generator solution. Tradeoffs:
- predictable output due to the hash function
- higher probability of a collision
In summary, don't use hashing function solution

##### Token Range Solution
An internal counter function of the token service generates the short URL and the output is monotonically increasing.
![[Pasted image 20241203215511.png]]
The token service must be horizontally partitioned (shard) to meet the scalability requirements of the system.

#### Read Path
![[Pasted image 20241203220247.png]]
Cache is introduced at following layers:
1. Client
2. CDN
3. Reverse Proxy
4. Dedicated Cache Servers
A shared cache such as CDN reduces load on the system however private cache is only accessible by the client and doesn't significantly improve system's performance. Also the definition of TTL for private cache is crucial because private cache invalidation is difficult. Dedicated cache servers are provisioned between following components:
1. server and data store (database)
2. load balancer and server

The cache servers are scaled out by performing following operations:
- partition the cache servers (use the short URL as the partition key)
- replicate the cache servers to handle heavy loads using [leader-follower](https://redis.io/docs/management/replication/) topology
- redirect the write operations to the leader
- redirect all the read operations to the follower replicas
![[Pasted image 20241203220729.png]]
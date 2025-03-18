### What is this ?
The back of the envelope are estimates generated using a combination of thought experiments and performance numbers through simple arithmetic. The performance numbers of the services involved should be known to conduct back of the envelope calculation. The unknown performance numbers can be determined through prototyping. For instance, measuring the write performance of the cache server can be determined through a prototype.
Some reasons to perform this:
- decide whether the database should be partitioned
- evaluate the feasibility of a proposed architectural design
- identify potential bottlenecks in the system
---
### Powers of Two 
Power of two is a handy reference to perform the back of the envelope

| Power | Exact Value | Approx Value | Bytes |
| ----- | ----------- | ------------ | ----- |
| 7     | 128         |              |       |
| 8     | 256         |              |       |
| 10    | 1024        | 1 thousand   | 1 KB  |
| 16    | 65536       |              | 64 KB |
| 20    | 1048576     | 1 million    | 1 MB  |
| 30    | 1073741824  | 1 billion    | 1 GB  |
| 32    | 4294967296  |              | 4 GB  |

---
### Availability Numbers
High availability is the ability of a service to remain reachable and not lose data even when a failure occurs. High availability is typically measured in terms of a percentage of uptime over a given period. The uptime is the amount of time that a system or service is available and operational.

| Availability (Percentage) | Downtime Per Year |
| ------------------------- | ----------------- |
| 99                        | 3.6 Days          |
| 99.99                     | 52 minutes        |
| 99.999                    | 5 minutes         |
| 99.9999                   | 31 seconds        |

In addition to measuring uptime, high availability can also be measured by other metrics such as mean time between failures (**MTBF**), which measures the average time between system failures, and mean time to repair (**MTTR**), which measures the average time it takes to restore a system after a failure.

A service-level agreement (**SLA**) is an explicit or implicit contract between a service provider and the users. The SLA documents the set of services the service provider will offer to the user and defines the service standards the provider is obligated to fulfil.

---
### Types of Back of the Envelope Calculations in System Design Interviews

The most common types of back of the envelope calculations in system design interviews are the following:
- traffic estimation
- storage estimation
- memory estimation
- bandwidth estimation
- resource estimation
- latency estimation

1. **Latency Estimation:** for sequential data fetch = latency of resource 1 + latency of resource 2, for parallel data fetch = max (latency of resource 1, latency of resource 2)
2. **Resource Estimation:** You can assume that a modern server contains 32 cores and the system latency should be less than 250 ms for a realtime experience.
   Formula for calculating Query per Second (QPS):
   `QPS = number of CPU cores / average time for a request in seconds`
   The initial design should focus on supporting the average traffic because it is the usual condition. The peak traffic must also be considered to make the system fault tolerant and highly available. The different approaches to determining peak traffic are the following:
	- 10% per hour rule
	- peak traffic = 2 * average traffic
	With the 10% per hour rule, when daily traffic is 1 million, you can assume that 10% of the daily traffic occurs within 1 hour. This would result in approximately 30 QPS. Alternatively, you should discuss with your interviewer whether it’s fine to assume the peak traffic to be twice the average traffic.

---
### Capacity Estimation Example
#### Traffic
[[URL Shortener]] is a read-heavy service.

| Description                   | Value        |
| ----------------------------- | ------------ |
| DAU (write)                   | 100 million  |
| QPS (write)                   | 1000         |
| read:write                    | 100:1        |
| QPS (read)                    | 100 thousand |
| time of persistence for a URL | 5 years      |

#### Storage
The shortened URL persists by default for 5 years in the data store. Each character size is assumed to be 1 byte. A URL record is approximately 2.5 KB in size. Set the replication factor of the storage to a value of at least three for improved durability and disaster recovery.
![[Pasted image 20241202221840.png]]
#### Bandwidth
Ingress is the network traffic that enters the server (client requests).
![[Pasted image 20241202221939.png]]
Egress is the network traffic that exits the servers (server responses).
![[Pasted image 20241202221955.png]]
#### Memory
The URL redirection traffic (egress) is cached to improve the latency. Following the 80/20 rule, 80% of egress is served by 20% of URL data stored on the cache servers. The remaining 20% of the egress is served by the data store to improve the latency. A TTL of 1 day is reasonable.
![[Pasted image 20241202222237.png]]

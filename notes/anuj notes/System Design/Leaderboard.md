## How does leaderboard work ?
The Redis sorted set is the data type for the use cases and access patterns in the leaderboard requirements. The sorted set is an in-memory data type that makes it trivial to generate the leaderboard in real-time for millions of players. The current rank of the players can be fetched in logarithmic time. In simple words, the leaderboard is a set sorted by the score. The score and leaderboard records are persisted on the relational database as well to support complex queries.

---
## Questions to ask the interviewer
1. What are the primary use cases of the system ?
	Update the score and and display the leaderboard
2. Are the clients distributed along the globe ?
	Yes
3. What is the amount of Daily Active Users for writes ?
	50 Million DAU
4. What is the anticipated read:write ratio ?
	5:1
5. Should the leaderboard be available in real time ?
	Yes
---
## Requirements
### Functional Requirements
- The **client** (player) can view the top 10 players on the leaderboard in real-time (absolute leaderboard)
- The client can view a specific player’s rank and score
- The client can view the surrounding ranked players to a particular player (relative leaderboard)
- The client can receive score updates through push notifications
- The leaderboard can be configured for global, regional, and friend circles
- The client can view the historical game scores and historical leaderboards
- The leaderboards can rank players based on gameplay on a daily, weekly, or monthly basis
- The clients can update the leaderboard in a fully distributed manner across the globe
- The leaderboard should support thousands of concurrent players
### Non Functional Requirements
- High availability
- Low latency
- Scalability
- Reliability
- Minimal operational overhead
---
## Leaderboard Data Storage

- A platform for coding quizzes where users see a realtime leaderboard
- Fetch quiz data from any external API
- use web-sockets or long polling for realtime leaderboard updation
- can use Redis to scale up web-sockets across multiple servers
- PostgreSQL for storing data
- Message Queues can be used to store data in DB
	- Obstacle -> How to handle realtime data updation when we can't store data in DB. (Message broker like kafka can be used in order to pass data to different services or we can use redis sorted set to maintain the realtime leaderboard and then later on it can be updated in DB, once the quiz is over)

### Deployment:
- try to deploy using [[Docker]]
- basically create a [[Docker Compose]] file which spins up both FE and BE and deploys on an EC2 instance

NOTE: Try coding the backend in [[Basics of Golang|GoLang]]
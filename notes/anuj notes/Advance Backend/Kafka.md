## Jargons
- **Cluster and Broker:** A group of machines running kafka are known as a kafka cluster. Each individual machine is called a broker
- **Producers:** Used to `publish` the data to a topic
- **Consumers:** Consume from a topic
- **Topics:** A logical channel to which producers send messages and from which consumers read messages.
- **Offsets:** Consumers keep track of their position in the topic by maintaining offsets, which represent the position of the last consumed message. Kafka can manage offsets automatically or allow consumers to manage them manually.
- **Retention:** Kafka topics have configurable retention policies, determining how long data is stored before being deleted. This allows for both real-time processing and historical data replay.
## Problem Statement ?
Ops in a DB or throughput of a DB is not high, hence we can't simply store realtime data in DB or else that'll eventually crash the DB
## How Kafka solves the Problem ?
- Has a very high throughput, the catch over here is that Kafka has high throughput but less storage. Hence both DB & Kafka rely on each other.
- Querying large datasets is not optimal in Kafka or other message queues because they don't support indexing as in 
#### Example for Uber like application:
![[Uber Kafka Example.excalidraw]]
#### Kafka Components
Example using Zomato like Application
![[Kafka Components.excalidraw]]
Kafka does **auto-balancing** for Consumers

1 Consumer -> Multiple Partitions
1 Partition -> 1 Consumer
![[Consumer Partition Relation in Kafka.excalidraw]]
To overcome the above problem, we have **Consumer Groups**.
![[Consumer Groups Kafka.excalidraw]]
Basically Self Balancing is done on Group Level

---
## Starting Kafka locally
**Prerequisites: [[Docker]]**
- Start [[Zookeeper]] in a docker container
  `docker run -p 2181:2181 zookeeper`
- Start Kafka Container
```bash
docker run -p 9092:9092 \
-e KAFKA_ZOOKEEPER_CONNECT=<PRIVATE_IP>:2181 \
-e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://<PRIVATE_IP>:9092 \
-e KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=1 \
confluentinc/cp-kafka
```

### Basic Code Example:
`client.js`
```js
const { Kafka } = require("kafkajs");

exports.kafka = new Kafka({
  clientId: "my-app",
  brokers: ["<PRIVATE_IP>:9092"],
});
```
`admin.js`
```js
const { kafka } = require("./client");

async function init() {
  const admin = kafka.admin();
  console.log("Admin connecting...");
  admin.connect();
  console.log("Adming Connection Success...");

  console.log("Creating Topic [rider-updates]");
  await admin.createTopics({
    topics: [
      {
        topic: "rider-updates",
        numPartitions: 2,
      },
    ],
  });
  console.log("Topic Created Success [rider-updates]");

  console.log("Disconnecting Admin..");
  await admin.disconnect();
}

init();
```
`producer.js`
```js
const { kafka } = require("./client");
const readline = require("readline");

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

async function init() {
  const producer = kafka.producer();

  console.log("Connecting Producer");
  await producer.connect();
  console.log("Producer Connected Successfully");

  rl.setPrompt("> ");
  rl.prompt();

  rl.on("line", async function (line) {
    const [riderName, location] = line.split(" ");
    await producer.send({
      topic: "rider-updates",
      messages: [
        {
          partition: location.toLowerCase() === "north" ? 0 : 1,
          key: "location-update",
          value: JSON.stringify({ name: riderName, location }),
        },
      ],
    });
  }).on("close", async () => {
    await producer.disconnect();
  });
}

init();
```
`consumer.js`
```js
const { kafka } = require("./client");
const group = process.argv[2];

async function init() {
  const consumer = kafka.consumer({ groupId: group });
  await consumer.connect();

  await consumer.subscribe({ topics: ["rider-updates"], fromBeginning: true });

  await consumer.run({
    eachMessage: async ({ topic, partition, message, heartbeat, pause }) => {
      console.log(
        `${group}: [${topic}]: PART:${partition}:`,
        message.value.toString()
      );
    },
  });
}

init();
```
**Running Locally**
- Run multiple consumers
	`node consumer.js <GROUP_NAME>`
- Create Producer
	`node producer.js`
```shell
> tony south
> tony north
```
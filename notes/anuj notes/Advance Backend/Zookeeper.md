## What is Zookeeper?
A distributed, open-source coordination service for distributed applications. It exposes a simple set of primitives to implement higher-level services for synchronization, configuration maintenance, and group and naming.
In a distributed system, there are multiple nodes or machines that need to communicate with each other and coordinate their actions. ZooKeeper provides a way to ensure that these nodes are aware of each other and can coordinate their actions. It does this by maintaining a hierarchical tree of data nodes called “**Znodes**“, which can be used to store and retrieve data and maintain state information.
## Why do we need it ?
- **Coordination Services:** The integration/communication of services in a distributed environment. Coordination services are complex to get right. They are especially prone to errors such as race conditions and deadlock.
- **Race Condition:** Two or more systems trying to perform the same task.
- **Deadlocks:** Two or more operations waiting for each other.
  
To make the coordination between distributed environments easy, developers came up with an idea called zookeeper so that they don’t have to relieve distributed applications of the responsibility of implementing coordination services from scratch.

Microservice is a small, loosely coupled service that is designed to perform a specific business function and each microservice can be developed, deployed, and scaled independently.

### Main components of microservices architecture
- **Microservices:** Small, loosely coupled services that handle specific functions
- **API Gateway:** acts as a central entry point for external clients. They also manage requests, authentication and route the requests to the appropriate microservice
- **Service Registry and Discovery:** Keeps track of the locations and addresses of all microservices, enabling them to locate and communicate with each other dynamically.
- **[[Horizontal Scaling on AWS|Load Balancer]]:** Distributes incoming traffic across multiple service instances and prevent any of the microservice from being overwhelmed.
- **[[Containerization]]:** [[Docker]] encapsulate microservices and their dependencies and orchestration tools like [[Kubernetes]] manage their deployment and scaling.
- **Event Bus/Message Broker:** Facilitates communication between microservices, allowing pub/sub asynchronous interaction of events between components/microservices, e.g. [[Messaging Queues]].
- **Database per microservice:** Each microservice usually has its own database, promoting data autonomy and allowing for independent management and scaling.
- **Caching:** Cache stores frequently accessed data close to the microservice which improved performance by reducing the repetitive queries.
- **Fault Tolerance & Resiliency Components:** Components like circuit breakers and retry mechanisms ensure that the system can handle failures gracefully, maintaining overall functionality
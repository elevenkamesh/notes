The node that takes care of deploying the containers/healing them/listening to the developer to understand what to deploy. 
![[Kubernetes Master Node (Control Pane).png]]
## API Server
- **Handling Restful API requests:** This processes and responds to API requests from various clients and `kubectl` CLI tool, other Kubernetes components and external applications. These requests involve CRUD operations on Kubernetes resources such as pods, services and deployments.
- **Authentication & Authorization:** This manages auth of all API requests. Only authenticated and authorized users can perform operations on the cluster.
- **Metrics & Health Checks:** Exposes metrics and health check endpoints to monitor and diagnose the health and perf of control pane.
- **Communication Hub:** Acts as central communication hub for the kubernetes control pane. Every component interacts with this to retrieve and update the state of cluster.
## etcd
Consistent and highly-available key value store used as Kubernetes' backing store for all cluster data.
## Kube-Scheduler
Control plane component that watches for newly created Pods with no assigned node, and selects a node for them to run on. Its responsible for pod placement and deciding which pod goes on which node.
## Kube-Controller-Manager
Component of the control pane that runs a set of controllers, each of which is responsible to manage a particular aspect of the state of the cluster. Some examples of controllers are:
- **Node controller:** Notices and responds when nodes go down.
- **Deployment Controller:** Watches for newly created or updated deployments and manages ReplicaSets based on deployment specifications. Ensures that desired state of deployment is maintained by creating or scaling ReplicaSets as needed.
- **ReplicaSet Controller:** Watches for newly created or updated ReplicaSets & ensures that desired number of pod replicas are running at any given point of time. It creates or deletes pods to maintain the specified number of Replicas in the ReplicaSet’s configuration
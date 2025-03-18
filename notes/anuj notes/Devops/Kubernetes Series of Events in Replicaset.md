User creates a `deployment` which creates a `replicaset` which creates `pods` If `pods` go down, `replicaset controller` ensures to bring them back up.
![[Kubernetes Series of events in replicaset.png]]
### Step by Step breakdown
When we run the following command, a bunch of things happen:
`kubectl create deployment nginx-deployment --image=nginx --port=80 --replicas=3`
- **Command Execution**
	- You execute a command on a machine with `kubectl` installed and configured to interact with your kubernetes cluster
- **API Request**
	- `kubeclt` sends a request to the Kubernetes API server to create a deployment resource with the specified parameters.
- **API Server Processing**
	- The API server receives the request, validates it, and then processes it. If the request is valid, the API server updates the desired state of the cluster stored in etcd. The desired state now includes the new Deployment resource.
- **Storage in etcd**
	- The Deployment definition is stored in etcd, the distributed key-value store used by Kubernetes to store all its configuration data and cluster state. etcd is the source of truth for the cluster's desired state.
- **Deployment Controller Monitoring**
	- The deployment controller, which is part of the `kube-controller-manager`, continuously watches the API server for changes to deployments. It detects the new deployment you created.
- **Replicaset Creation**
	- The Deployment controller creates a ReplicaSet based on the Deployment's specification. The ReplicaSet is responsible for maintaining a stable set of replica Pods running at any given time.
- **Pod Creation**
	- The ReplicaSet controller (another part of the `kube-controller-manager`) ensures that the desired number of Pods (in this case, 3) are created and running. It sends requests to the API server to create these Pods.
- **Scheduler Assignment**
	- The Kubernetes scheduler watches for new Pods that are in the "Pending" state. It assigns these Pods to suitable nodes in the cluster based on available resources and scheduling policies.
- **Node and Kubelet**
	- The kubelet on the selected nodes receives the Pod specifications from the API server. It then pulls the necessary container images (nginx in this case) and starts the containers.
### Hierarchical Relationship 
- **Deployment**
	- **High-Level Manager**: A Deployment is a higher-level controller that manages the entire lifecycle of an application, including updates, scaling, and rollbacks.
	- **Creates and Manages ReplicaSets**: When you create or update a Deployment, it creates or updates ReplicaSets to reflect the desired state of your application.
	- **Handles Rolling Updates and Rollbacks**: Deployments handle the complexity of updating applications by managing the creation of new ReplicaSets and scaling down old ones.
- **ReplicaSet**:
	- **Mid-Level Manager**: A ReplicaSet ensures that a specified number of identical Pods are running at any given time.
	- **Maintains Desired State of Pods**: It creates and deletes Pods as needed to maintain the desired number of replicas.
	- **Label Selector**: Uses label selectors to identify and manage Pods.
-  **Pods**:
	- **Lowest-Level Unit**: A Pod is the smallest and simplest Kubernetes object. It represents a single instance of a running process in your cluster and typically contains one or more containers.
## What is Kubernetes? 
- Container orchestration engine
- Lets you create, delete and update `containers`
- Useful when:
	- You have your docker images in the docker registry and want to deploy it in a `cloud native` fashion.
	- You want to `not worry about` patching, crashes. You want the system to `auto heal`
	- You want to autoscale with some simple constructs
	- You want to observe your complete system in a simple dashboard
	![[K8s Cluster.png]]
- Without using Kubernetes, following is how our application will look like:
	- Backend
	![[Backend wo kubernetes.png]]
	- Frontend
	![[Frontend wo Kubernetes.png]]
	- In K8, frontend & backend all are pods in a `kubernetes cluster`
	![[FE & BE with Kuberenetes.png]]
## Jargons
### Nodes
We can create & connect various machines together (all are running Kubernetes). Every machine is known as a `node`. There are 2 types of nodes:
- **[[Kubernetes Master Node (Control Pane)|Master Node (Control Pane)]]**
- **[[Kubernetes Worker Nodes|Worker Nodes]]**
### Cluster
A bunch of master and worker nodes make up `kubernetes cluster`. We can add/remove nodes from a cluster.
### Images
A docker image is a lightweight, standalone, and executable software package that includes everything needed to run a piece of software. Images are built from a set of instructions defined in a file called a Dockerfile.
### [[Docker|Containers]]
### Pods
A pod is the simplest and smallest unit in the kubernetes object model that you can create or deploy.
**Basically following is how each element relates to one another:**
Cluster → Multiple Nodes → Each node can run multiple pods → Each pod can have multiple containers running → Each container has an image which is being pulled from dockerhub

![[Kubernetes Pod.png]]
## Kubernetes Command Basics (Kubectl & Kind)
### Single Node setup
- Create a 1-node cluster
```bash
kind create cluster --name local
```
If we check our docker containers, a single container (control-pane) will be running.
- Delete the cluster
```bash
kind delete cluster -n local
```
### Multi Node Setup
- Create a `clusters.yml` file
```YAML
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
```
- Create the node setup
```bash
kind create clusters --config clusters.yml --name local
```
If we check docker containers, we’ll see 3 containers running in total. (local-control-plane, local-worker, local-worker-2). Hence we’ve a node cluster running locally. We can also call the API on the PORT that’s exposed via control-plane on docker.
e.g. endpoint [https://127.0.0.1:50949/api/v1/namespaces/default/pods](https://127.0.0.1:50949/api/v1/namespaces/default/pods)
Kubernetes API server does authentication checks and prevents us from getting in. All of the authentication creds are stored by `kind` in `~/.kube/config`
### Start a Pod using K8s
- Start a Pod
```bash
kubectl run nginx --image=nginx --port=80
```
- Check the status of pod
```bash
kubectl get pods
```
- Check the logs
```bash
kubectl logs nginx
```
We can also `kubectl describe pod nginx` to get more details
**What are system looks like right now ?**
![[System after starting a pod locally.png]]
### Stop the pod
```bash
kubectl delete pod nginx
```
### Kubernetes Manifest
A manifest defines the desired state for Kubernetes resources, such as Pods, Deployments, Services, etc., in a declarative manner.
**Original Command**
```bash
kubectl run nginx --image=nginx --port=80
```
**Manifest**
```YAML
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```
**Breakdown of the manifest**
![[Kubernetes manifest breakdown.png]]
**Apply the manifest**
```bash
kubectl apply -f manifest.yml
```
**Delete the pod**
```bash
kubectl delete pod nginx
```

## Deployment
A **deployment** in kubernetes is a higher-level abstraction that manages a set of Pods and provides a declarative updates to them. It offers features like scaling, rolling updates, and rollback capabilities, making it easier to manage the lifecycle of applications.
![[Kubernetes deployment.png]]
**[[Differences between Kubernetes Deployment and Pod]]**
**[[Kubernetes Creating Deployment|Creating a Deployment]]**
### Why do we need deployment ?
If all that a `deployment` does is create a `replicaset` , why cant we just create `rs` ?
#### Experiment
Update the `image` to be `nginx2` (an image that doesn't exist)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx2:latest
        ports:
        - containerPort: 80
```
- Apply the new deployment
	`kubectl apply -f deployment.yml`
- Check the new rs now
	```bash
	kubectl get rs
	NAME                          DESIRED   CURRENT   READY   AGE
	nginx-deployment-576c6b7b6    3         3         3       14m
	nginx-deployment-5fbd4799cb   1         1         0       10m
	```
- Check the pods
	```bash
	kubectl get pods
	NAME                                READY   STATUS             RESTARTS   AGE
	nginx-deployment-576c6b7b6-9nlnq    1/1     Running            0          15m
	nginx-deployment-576c6b7b6-m8ttl    1/1     Running            0          16m
	nginx-deployment-576c6b7b6-n9cx4    1/1     Running            0          16m
	nginx-deployment-5fbd4799cb-fmt4f   0/1     ImagePullBackOff   0          12m
	```

## Replicaset
A ReplicaSet in Kubernetes is a controller that ensures a specified number of pod replicas are running at any given time. It is used to maintain a stable set of replica Pods running in the cluster, even if some Pods fail or are deleted.
When you create a deployment, you mention the amount of `replicas` you want for this specific pod to run. The deployment then creates a new `ReplicaSet` that is responsible for creating X number of pods.
![[Kubernetes Replicaset controller.png]]
**[[Kubernetes Series of Events in Replicaset|Series of Events happening in Replicaset]]**
**[[Kubernetes Creating a Replicaset|Create a Replicaset]]**

## Why do we need deployment ?
If all that a `deployment` does is create a `replicaset` , why cant we just create `rs` ?
### Experiment
Update the `image` to be `nginx2` (an image that doesn't exist)
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx2:latest
        ports:
        - containerPort: 80
```
- Apply the new deployment
	`kubectl apply -f deployment.yml`
- Check the new rs now
	```bash
	kubectl get rs
	NAME                          DESIRED   CURRENT   READY   AGE
	nginx-deployment-576c6b7b6    3         3         3       14m
	nginx-deployment-5fbd4799cb   1         1         0       10m
	```
- Check the pods
	```bash
	kubectl get pods
	NAME                                READY   STATUS             RESTARTS   AGE
	nginx-deployment-576c6b7b6-9nlnq    1/1     Running            0          15m
	nginx-deployment-576c6b7b6-m8ttl    1/1     Running            0          16m
	nginx-deployment-576c6b7b6-n9cx4    1/1     Running            0          16m
	nginx-deployment-5fbd4799cb-fmt4f   0/1     ImagePullBackOff   0          12m
	```
#### Role of Deployment
Deployment ensures that there is a smooth deployment, and if the new image fails for some reason, the old replicaset is maintained. Even though the `rs` is what does `pod management`, `deployment` is what does `rs management`.

**Rollbacks**
- Check the history of deployments
	`kubectl rollout history deployment/nginx-deployment`
- Undo the last deployment
	`kubectl rollout undo deployment/nginx-deployment`

### Create a new deployment
- Replace the image to be of `postgres`
	```yaml
	apiVersion: apps/v1
	kind: Deployment
	metadata:
	  name: nginx-deployment
	spec:
	  replicas: 3
	  selector:
	    matchLabels:
	      app: nginx
	  template:
	    metadata:
	      labels:
	        app: nginx
	    spec:
	      containers:
	      - name: nginx
	        image: postgres:latest
	        ports:
	        - containerPort: 80
	        env:
	        - name: POSTGRES_PASSWORD
	          value: "yourpassword"
	```
## How to expose the app ?
Let's delete all the resources and restart a `deployment` for `nginx` with 3 replicas
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```
- Apply the config
	`kubeclt apply -f deployment.yml`
- Get all pods
```bash
kubectl get pods -owide
NAME                               READY   STATUS    RESTARTS   AGE     IP            NODE            NOMINATED NODE   READINESS GATES
nginx-deployment-576c6b7b6-7jrn5   1/1     Running   0          2m19s   10.244.2.19   local-worker2   <none>           <none>
nginx-deployment-576c6b7b6-88fkh   1/1     Running   0          2m22s   10.244.1.13   local-worker    <none>           <none>
nginx-deployment-576c6b7b6-zf8ff   1/1     Running   0          2m25s   10.244.2.18   local-worker2   <none>           <none>
```
- The IPs that you see are `private IPs`. You won't be able to access the app on it.
To expose the app, you need to use [[#Services]]
## Services
In kubernetes, a "Service" is an abstraction that defines a logical set of Pods and a policy by which to access them. Kubernetes Services provide a way to expose applications running on a set of Pods as network services. Here are the key points about Services in Kubernetes:
1. **Pod Selector:** Services use labels to select the Pods they target. A label selector identifies a set of Pods based on their labels.
2. **Service Types:** 
	1. **Cluster IP:** Exposes the Service on an internal IP in the cluster. This is the default ServiceType. The Service is only accessible within the cluster.
	2. **NodePort:** Exposes the Service on each Node’s IP at a static port (the NodePort). A ClusterIP Service, to which the NodePort Service routes, is automatically created. You can contact the NodePort Service, from outside the cluster, by requesting `<NodeIP>:<NodePort>`.
	3. **Loadbalancer:** Exposes the Service externally using a cloud provider’s load balancer. NodePort and ClusterIP Services, to which the external load balancer routes, are automatically created.
3. **Endpoints:** These are automatically created and updated by Kubernetes when the Pods selected by a Service's selector change.
- Create Service.yml
	```yaml
	apiVersion: v1
	kind: Service
	metadata:
	  name: nginx-service
	spec:
	  selector:
	    app: nginx
	  ports:
	    - protocol: TCP
	      port: 80
	      targetPort: 80
	      nodePort: 30007  # This port can be any valid port within the NodePort range
	  type: NodePort
	```
- Restart the cluster with a few extra ports exposed (create kind.yml)
	```yaml
	kind: Cluster
	apiVersion: kind.x-k8s.io/v1alpha4
	nodes:
	- role: control-plane
	  extraPortMappings:
	  - containerPort: 30007
	    hostPort: 30007
	- role: worker
	- role: worker
	```
- `kind create cluster --config kind.yml`
- Re-apply the deployment and the service
- Visit `localhost:30007`
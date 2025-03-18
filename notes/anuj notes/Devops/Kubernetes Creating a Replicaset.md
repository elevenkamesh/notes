- Create rs.yml
	```yaml
	apiVersion: apps/v1
	kind: ReplicaSet
	metadata:
	  name: nginx-replicaset
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
- Apply the manifest
	`kubectl apply -f rs.yml`
- Get the rs details
	```bash
	kubectl get rs
	
	NAME               DESIRED   CURRENT   READY   AGE
	nginx-replicaset   3         3         3       23s
	```
- Check the pods
	```bash
	kubectl get pods
	
	NAME                     READY   STATUS    RESTARTS   AGE
	nginx-replicaset-7zp2v   1/1     Running   0          35s
	nginx-replicaset-q264f   1/1     Running   0          35s
	nginx-replicaset-vj42z   1/1     Running   0          35s
	```
- Try deleting a pod and check if it self heals
	```bash
	kubectl delete pod nginx-replicaset-7zp2v
	kubectl get pods
	```
- Try adding a pod with `app=nginx`
	`kubectl run nginx-pod --image=nginx --labels="app=nginx"`
- Ensure it gets terminated immediately because the rs already has 3 pods running
- Delete the replicaset
	`kubectl delete rs nginx-deployment-<id>`
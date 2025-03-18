Let's create a deployment that creates 3 pods
- Create a deployment.yml
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
- Apply the deployment
	`kubectl apply -f deployment.yml`
- Get the deployment
	```bash
	kubectl get deployment
	
	NAME               READY   UP-TO-DATE   AVAILABLE   AGE
	nginx-deployment   3/3     3            3           18s
	```
- Get the rs
	```bash
	kubectl get rs
	NAME                         DESIRED   CURRENT   READY   AGE
	nginx-deployment-576c6b7b6   3         3         3       34s
	```
- Get the pod
	```bash
	kubectl get pod
	NAME                               READY   STATUS    RESTARTS   AGE
	nginx-deployment-576c6b7b6-b6kgk   1/1     Running   0          46s
	nginx-deployment-576c6b7b6-m8ttl   1/1     Running   0          46s
	nginx-deployment-576c6b7b6-n9cx4   1/1     Running   0          46s
	```
- Try deleting a pod
	`kubectl delete pod nginx-deployment-576c6b7b6-b6kgk`
- Ensure the pods are still up
	`kubectl get pods`
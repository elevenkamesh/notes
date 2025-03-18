The nodes that actually run your BE/FE.
![[Kubernetes Worker Node.png]]
## Kubelet
An agent that runs on each node in the cluster. It makes sure that containers are running in a Pod
**How the kubelet control loop works ?**
1. **Watch for PodSpecs:** the kubelet watches the API server for PodSpecs that are scheduled to run on its node. This includes both new pods that need to be started and existing pods that may need to be updated or terminated.
2. **Reconcile Desired State:** The kubelet compares the current state of the node (which pods are running, their statuses, etc.) with the desired state as defined by the PodSpecs.
3. **Take Action:** Based on this comparison, the kubelet takes actions to reconcile the actual state with the desired state:
	1. **Start Pods:** if there are new PodSpecs, the kubelet will pull the necessary container images, create the containers, and start the pods.
	2. **Monitor Health:** The kubelet performs health checks (liveness and readiness probes) on running containers. If a acontainer fails a health check, the kubelet may restart it according to the pod’s restart policy.
	3. **Update Pods:** if there are changes to the PodSpecs (e.g. configuration changes), the kubelet will update the running pods accordingly.
	4. **Stop Pods:** If a pod is no longer needed or should be terminated, the kubelet will stop and remove the containers.
4. **Report Status:** The kubelet periodically reports the status of the pods and the node back to the API server. This includes resource usage (CPU, memory, etc.) and the status of each container
## Kube-Proxy
The `kube-proxy` is a network proxy that runs on each node in a Kubernetes cluster. It is responsible for you being able to talk to a pod.
![[Kube-Proxy.png]]
## Container Runtime
In a kubernetes worker node, the container runtime is the software responsible for running containers.
It interacts with the kubelet, which is the agent running on each node, to manage the lifecycle of containers as specified by Kubernetes pod specifications. The container runtime ensures that the necessary containers are started, stopped and managed correctly on the worker node.
**Common Container Runtimes for Kubernetes**
1. containered
2. CRI-O
3. Docker

**Kubernetes Container Runtime Interface (CRI)**
The Container Runtime Interface (CRI) is a plugin interface that allows the kubelet to use a variety of container runtimes without needing to know the specifics of each runtime. This abstraction layer enables Kubernetes to support multiple container runtimes, providing flexibility and choice to users.
![[Kubernetes CRI.png]]


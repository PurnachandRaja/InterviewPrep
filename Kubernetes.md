### 1.What are some use cases of Kubernetes?
Microservices. Let us say we have presentation layer, application layer, and database. We expose presentation layer pods with service or loadbalancer Ingress. Also the traffic between presentation & application layer is routed using service or loadbalancer Ingress 

Use case:
Running tools on kubernetes
Jenkins, GitLab CI

### 2. What is the difference between daemonset and sidecar container?
The main difference is that DaemonSets ensure that a specific Pod runs on all nodes in the cluster, while sidecar containers run alongside the primary container in the same Pod, providing additional functionality or services to the primary container.

### 3.What is the difference between secret and configmap?
Secrets are used to store and manage sensitive information, such as passwords or API keys, while config maps are used to store and manage configuration data for applications. The main difference is that secrets are base64-encoded and encrypted, while config maps are not.

### 4. Why should I choose EKS/AKS/GKE rather than running on VM? Missing feature?
Managed Kubernetes services like EKS, AKS, and GKE simplify cluster management, provide automated upgrades, integrate with other cloud services, and offer scalability, high availability, and security features.

Some of the missing features in AKS (Azure Kubernetes Service) include:

Limited support for custom Kubernetes versions
Limited control over node upgrades and scaling

### 5.What is the  use of ETCD ?
etcd is a distributed key-value store used as a data store for Kubernetes, providing a reliable and highly available way to store and manage configuration data, stateful information, and metadata for a Kubernetes cluster.

 ### 6.How do i take backup of Kubernetes cluster?
 - Kubernetes Objects and configs stored in etcd
   - Cloud Providers take backup
   - Possible to take backup using tools

- Apllication data stored in persistent volumes
  - Can be backed up using K8s snapshot APIs
  - Use tools

### 7.What is the difference between ingress & load balancer in Kubernetes?
The LoadBalancer service and Ingress are both used for managing external traffic to Kubernetes Cluster, but they serve different purposes.
LoadBalancer service is used to provide load balancing for a set of pods and expose them externally, while Ingress is used for managing external access to services in the cluster and providing more advanced traffic management capabilities. Ingress can also be used to manage multiple services with a single IP address and port. 

### 8.What is the purpose of the Kubernetes Taints & Tolerations ?
Taints and tolerations in Kubernetes are used to mark nodes with certain attributes and schedule Pods on nodes that have the corresponding tolerations. The purpose is to ensure that only specific Pods are scheduled on specific nodes, and to provide a mechanism for nodes to reject certain Pods that do not have the corresponding tolerations.

### 9.What is the purpose of Node affinity/ Anti-pod affinity ?
Node affinity and anti-pod affinity are used in Kubernetes to schedule Pods on specific nodes or avoid scheduling them on specific nodes based on certain criteria. The purpose is to ensure that Pods are scheduled on nodes that meet specific requirements or to spread them across different nodes for high availability and fault tolerance.

### 10.What is the use of kubernetes Headless service?
The purpose of a Headless Service is to enable direct communication between client applications and individual Pods. This can be useful in scenarios where you need to perform advanced networking configurations, such as setting up a distributed database cluster, where each database instance needs to communicate directly with other instances.

### 4.What is the use of Statefulsets & Statelesssets?
StatefulSets and StatelessSets are two different ways of managing applications in Kubernetes based on their stateful or stateless nature. If your application requires stable network identities and persistent storage, then you should use a StatefulSet. If your application is stateless and can scale up or down easily without requiring any changes to its storage or network configuration, then you should use a StatelessSet.

### 5.What are the different types of Services in Kubernetes ?
In Kubernetes, there are four different types of services that can be used to expose applications running inside a cluster:

ClusterIP: This is the default type of service in Kubernetes. It provides a stable IP address and DNS name for accessing the service within the cluster. This type of service is typically used for internal communication between different components of an application.

NodePort: This type of service exposes the service on a specific port on each node in the cluster, which allows the service to be accessed from outside the cluster. This type of service is typically used for development and testing purposes.

LoadBalancer: This type of service exposes the service using a load balancer that is provided by the cloud provider. This allows the service to be accessed from outside the cluster using a public IP address. This type of service is typically used for production deployments.

ExternalName: This type of service maps the service to a DNS name outside the cluster. This is useful when you want to provide access to a service that is running outside the cluster, such as a database or a third-party API.

Each type of service has its own use case and benefits, and choosing the right type of service depends on the specific requirements of your application.


### 7.Kubernetes secrets use?
Kubernetes Secrets are a way to securely store and manage sensitive information, such as passwords, API keys, and certificates, in a Kubernetes cluster.

Using Kubernetes Secrets, you can provide your applications with the necessary credentials and other sensitive information without exposing them in your code, configuration files, or environment variables.

### 8.What is the difference between Kubernetes deployment & replication controller ?
Deployment provides more advanced deployment features, such as rolling updates and rollbacks, and uses ReplicaSets to manage the state of the application. A Replication Controller, on the other hand, is a simpler way of managing the state of an application and does not provide any guarantees about the order in which Pods are created or updated.

### 9.What is the purpose of the Ingress in Kubernetes?
It provides a way to route incoming traffic to different services based on the requested host or path.
The purpose of Ingress is to provide a single point of entry for external traffic into the Kubernetes cluster. Instead of exposing each service separately and managing different IP addresses or ports, Ingress allows for a unified entry point that can handle multiple services. This simplifies the management of external traffic and makes it easier to control access and security.

### 10.NodePort service port range ?
30000 - 32767

### 11.What is the differences between liveness probe vs readiness probe in kubernetes?
Liveness probes are used to determine if a container is running properly, while readiness probes are used to determine if a container is ready to receive traffic. The main difference is that liveness probes determine whether to restart the container, while readiness probes determine whether to send traffic to the container.

### 13.How to trouble shoot if the pod is not scheduled in Kubernetes ?
To troubleshoot a pod that is not scheduled in Kubernetes, you can:

Check the status of the pod with the kubectl describe pod command.
Check if the pod's required resources are available on the node.
Check if the pod's requested resources match the node's available resources.
Check if there are any taints on the nodes that prevent the pod from scheduling.
Check if there are any affinity or anti-affinity rules that prevent the pod from scheduling.
Check if there are any pod security policies that prevent the pod from scheduling.
Check if there are any network or storage issues preventing the pod from scheduling.
Check the Kubernetes logs for any error messages related to the pod scheduling.



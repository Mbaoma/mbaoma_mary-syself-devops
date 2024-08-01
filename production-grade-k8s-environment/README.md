## Architecture for your company’s new self-managed Kubernetes environment
The environment's requirements are
- Ubuntu 24.04 as the base OS
- Kubernetes version must be v1.30.3

### Environment Layers and their description
- Infrastructure Layer
- Control Plane Layer
- Worker Node Layer
- Networking Layer
- Storage Layer
- Security Layer
- Monitoring and Logging Layer

#### Infrastructure Layer
When you deploy Kubernetes to this layer, you create a cluster of working machines (called nodes) that can run your containerized applications. In this case, we have
- Ubuntu 24.04 as the base OS (VMs)
- Kubernetes version must be v1.30.3

#### Control Plane Layer
This layer manages the Kubernetes cluster. It consists of the following components: 

Components:
- API Server validates and configures data for API objects such as pods, services, and replication controllers.
- etcd is a consistent and highly-available key value store used as Kubernetes' backing store for all cluster data.
- Controller Manager embeds the core control loops shipped with Kubernetes.
- Scheduler ensures Pods are matched to Nodes so Kubelet can run them.

#### Worker Node Layer
This layer runs the application workloads.
Components:
- kubelet is the primary "node agent" that runs on each node.
- kube-proxy is a network proxy that runs on each node in a Kubernetes cluster.
- Container Runtime is responsible for managing the execution and lifecycle of containers within the Kubernetes environment e.g Docker, containerd.

#### Networking Layer
Clusters need a robust networking layer or pod communication and external access.
- CNI (Container Network Interface) plugin manages the network on which services operate.
- Ingress Controller is a dedicated load balancer for Kubernetes clusters.

#### Storage Layer
Stateful applications need reliable storage.
- CSI (Container Storage Interface): an interface for managing storage systems, e.g EBS CSI (for AWS EKS).
- Persistent Volumes (PVs): provides storage for pods.
- Persistent Volume Claims (PVCs): requests for storage on behalf of pods.

#### Security Layer
Security is integrated at various levels to protect the cluster and its workloads.
- RBAC (Role-Based Access Control) ensures that cluster users and workloads have access to resources they require to execute their roles.
- Network Policies allow you to specify how a pod is allowed to communicate with various network.
- Secrets and ConfigMaps: ConfigMaps stores data as key-value pairs, whereas Secrets stores data as base64-encoded data, thereby ensuring an additional layer of security.

#### Monitoring and Logging Layer
Monitoring and logging are critical for maintaining cluster health and troubleshooting.
- Prometheus collects and store metrics as time-series data.
- Grafana is an open source visualization tool used to view monitored metrics, logs, create dashboards and setup alerts.
- ELK Stack (Elasticsearch, Logstash, Kibana)

## Deployment and Configuration
To setup a Kubernetes cluster, I will use Terraform to setup the following components of my cluster
Bootstrap the Control Plane:
- Set up VMs for control plane components.
- Install and configure etcd, API server, controller manager, and scheduler.

Join Worker Nodes:
- Set up VMs for worker nodes.
- Install and configure kubelet, kube-proxy, and the container runtime.
- Join the worker nodes to the cluster using kubeadm or another tool.

Configure Networking:
- Deploy CNI plugins to enable pod-to-pod networking.
- Set up the ingress controller for external traffic management.

Set Up Storage:
- Install and configure the CSI drivers.
- Create storage classes, PVs, and PVCs as needed.

Implement Security Measures:
- Configure RBAC policies to restrict access.
- Define network policies to control traffic.
- Use Secrets and ConfigMaps to store sensitive data.

Setup monitoring and logging:
- Deploy Prometheus and Grafana for monitoring.
- Set up the ELK stack for centralized logging.

By following this architecture, I ensure a scalable, secure, and maintainable Kubernetes environment that meets the specified requirements while remaining cloud-agnostic.
# 🔷 Kubernetes Cluster Architecture: Deep Structure

![image](https://github.com/user-attachments/assets/cc5516d8-9cca-401c-8b2b-56331648ae11)


## 🔷 KUBERNETES CLUSTER

### 🧠 CONTROL PLANE (Master Node)

- **📡 API Server**  
  - **Parent of:** etcd, Scheduler, Controller Manager (Manages them by sending instructions)  
  - **Controlled by:** Master Node (Control Plane)  
  - **Handles Requests:** All user and system requests go through the API server (via `kubectl` or other clients).  
  - **Authentication/Authorization:** Validates and checks permissions before processing.  
  - **Serves the Cluster State:** Exposes a RESTful API for interaction.  
  - **Communicates with etcd, Scheduler, Controller Manager.**

- **🧾 etcd (Key-Value Database)**  
  - **Parent of:** N/A (a standalone component within the control plane)  
  - **Controlled by:** Master Node (Control Plane)  
  - **Stores Cluster Data:** Saves all data like cluster state, secrets, configuration, etc.  
  - **Highly Available & Distributed:** Data is stored redundantly to avoid data loss.  
  - **Critical for Cluster Recovery:** Can recover lost state or configuration during failures.  
  - **Interacts with API Server:** Holds and provides data when needed.

- **🎯 Scheduler**  
  - **Parent of:** Pods (instructs which node should run the pods)  
  - **Controlled by:** Master Node (Control Plane)  
  - **Assigns Pods to Nodes:** The scheduler looks at available nodes and assigns pods based on resource needs, constraints, etc.  
  - **Makes Smart Decisions:** Uses multiple factors to decide the best node (e.g., CPU, memory, affinity rules, taints, tolerations).  
  - **Communicates with Kubelet:** Instructs Kubelet to schedule pods onto nodes.  
  - **Interacts with API Server & Controller Manager.**

- **🛡️ Controller Manager**  
  - **Parent of:** Pods (Manages their desired state)  
  - **Controlled by:** Master Node (Control Plane)  
  - **Maintains Cluster State:** Ensures the current state of the cluster matches the desired state.  
  - **Control Loops:** Runs several control loops like ReplicaSet (ensures correct number of pods), Node (ensures nodes are healthy), Endpoints, etc.  
  - **Communication:** Works closely with the API Server and Scheduler to keep the cluster healthy.  
  - **Handles Failures:** Ensures new Pods are created if desired replicas are missing, or if Pods fail.  

- **☁️ Cloud Controller Manager (optional)**  
  - **Parent of:** Cloud Resources (manages cloud-specific infrastructure like load balancers)  
  - **Controlled by:** Master Node (Control Plane)  
  - **Integrates with Cloud Providers:** Manages cloud-specific resources (e.g., load balancers, storage, VM instances).  
  - **Handles Node Lifecycle:** Manages node addition/removal based on cloud resources.  
  - **Interacts with External Cloud Services:** Manages external IPs, volumes, etc.

---

### 🔨 WORKER NODES

- **📦 Kubelet**  
  - **Parent of:** Containers (manages the lifecycle of containers inside pods)  
  - **Controlled by:** Master Node (Control Plane)  
  - **Manages Pod Lifecycle:** Ensures that containers inside a Pod are running and healthy.  
  - **Communication with API Server:** Regularly checks with the API server for pod updates and reports node status.  
  - **Pod-to-Container Communication:** Ensures that containers inside Pods are running, restarted if they fail, and report back health statuses.  
  - **Manages Configurations:** Configures pods with environment variables, secrets, and config maps.

- **🌐 Kube-proxy**  
  - **Parent of:** N/A (works independently, manages network traffic)  
  - **Controlled by:** Master Node (Control Plane)  
  - **Service Networking:** Kube-proxy ensures that network traffic is correctly routed to Pods, whether internally or externally.  
  - **Load Balancing:** Routes traffic to multiple Pods in a service, balancing the load across all available Pods.  
  - **Service Discovery:** Helps in discovering services inside the cluster using ClusterIP, NodePort, and LoadBalancer.  
  - **IP Tables:** Uses iptables or IPVS to route traffic to Pods based on their IP addresses and port.

- **🧱 Container Runtime**  
  - **Parent of:** Containers (actual runtime for containers)  
  - **Controlled by:** Kubelet (on Worker Node)  
  - **Container Management:** Runs and manages the lifecycle of containers (Docker, containerd, or CRI-O).  
  - **Container Isolation:** Ensures that containers are isolated from each other but can communicate if required.  
  - **Pod Initialization:** Creates and manages containers as defined in Pods.  
  - **Supports Image Pulling:** Downloads and runs container images (Docker, containerd) from repositories (DockerHub, private registry).

- **🚀 Pods**  
  - **Parent of:** Containers (Pod is a collection of containers)  
  - **Controlled by:** Kubelet (on Worker Node)  
  - **Containers inside Pods:** Containers are grouped into Pods to run applications. A Pod can hold one or more containers.  
  - **Shared Network & Storage:** Containers within a pod share networking (localhost) and storage (volumes).  

  - **📥 Container Images**  
    - **Parent of:** Containers (images serve as blueprints for containers)  
    - **Controlled by:** Container Runtime (on Worker Node)  
    - **Blueprints for Containers:** Pre-packaged software including the app and all dependencies (e.g., Nginx, Redis, Java app).  
    - **Hosted on Registries:** Images are fetched from registries like DockerHub, GCR, or private registries.  
    - **Immutable:** Once built, images are immutable and cannot be changed.

---

## 📌 Key Parent-Child Relationships

1. **Master Node (Control Plane)**  
   - **Parent of:** API Server, etcd, Scheduler, Controller Manager, Cloud Controller Manager.  
   - **Controls:** All worker nodes and their components (Kubelet, Kube-proxy, etc.).

2. **Worker Node**  
   - **Parent of:** Kubelet, Kube-proxy, Container Runtime, Pods.  
   - **Controlled by:** Master Node (Control Plane).  
   - **Pods** contain **Containers** that run the application.  
   - **Containers** use **Container Images** to run.

---

## 📌 Summary of Key Interactions:
1. **API Server**: The entry point for all interactions. Requests come through the API Server and it talks to **etcd** for storage.
2. **etcd**: Holds all the cluster data and state, crucial for restoring or scaling the system.
3. **Scheduler**: Decides which nodes should run which pods based on the available resources and constraints.
4. **Controller Manager**: Ensures the desired state is maintained (e.g., if a pod dies, it’s automatically replaced).
5. **Kubelet**: Manages the lifecycle of pods on a worker node, ensuring they are healthy and running.
6. **Kube-proxy**: Ensures proper networking for services and routes requests to the right pod.

---

**Note:** The components within the **Control Plane** (Master Node) control the overall cluster state and orchestrate activities. The **Worker Nodes** execute the actual workloads and are controlled by the Master Node components like the **API Server** and **Scheduler**.


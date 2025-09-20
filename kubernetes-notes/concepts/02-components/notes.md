# ⚙️ Chapter 2: Kubernetes Components - The Heart of the Machine ⚙️

Namaste, Champion! Mana last chapter lo K8s enduku vachindo chusam. Ippudu, aa magic chese components gurinchi thelusukundam. Think of it like opening up a super-computer to see what's inside! Konchem concentration pedithe, idi chala easy. Let's go! 🔥

**What we will learn in this chapter:**
-   The two main parts of a K8s cluster: The **Control Plane** (The Brain 🧠) and the **Worker Nodes** (The Hands 🙌).
-   Prathi part lo unna individual components and vaati responsibilities.

Oka Kubernetes cluster lo manaki **Control Plane** and **Worker Nodes** untayi. Simple ga cheppalante, Control Plane anedi manager, and Worker Nodes anevi employees.

Ee diagram chudandi, high level lo architecture ela untundo clear ga ardham avthundi.

```mermaid
graph TD
    subgraph Control Plane (The Brain 🧠)
        direction LR
        API[kube-apiserver]
        ETCD[etcd]
        SCHED[kube-scheduler]
        CM[kube-controller-manager]
        CCM[cloud-controller-manager]

        API -- "Stores/Retrieves data" --> ETCD
        SCHED -- "Watches for new Pods" --> API
        CM -- "Watches for state changes" --> API
        CCM -- "Interacts with Cloud APIs" --> API
    end

    subgraph "Worker Node 1 (Employee 1)"
        direction TB
        KUBELET1[kubelet]
        PROXY1[kube-proxy]
        CR1[Container Runtime]
        CR1 --> POD1[Pod - App A]
        CR1 --> POD2[Pod - App B]
    end

    subgraph "Worker Node 2 (Employee 2)"
        direction TB
        KUBELET2[kubelet]
        PROXY2[kube-proxy]
        CR2[Container Runtime]
        CR2 --> POD3[Pod - App C]
    end

    API -- "Manages (Pod info, commands)" --> KUBELET1
    API -- "Manages (Pod info, commands)" --> KUBELET2
    KUBELET1 -- "Manages network rules" --> PROXY1
    KUBELET2 -- "Manages network rules" --> PROXY2

    style "Control Plane" fill:#d3d3d3,stroke:#333,stroke-width:2px
    style "Worker Node 1" fill:#f2f2f2,stroke:#333,stroke-width:2px
    style "Worker Node 2" fill:#f2f2f2,stroke:#333,stroke-width:2px
```

Ippudu prathi component gurinchi detail ga chuddam.

## A. Control Plane Components (The Manager/Brain 🧠)

Control Plane anedi cluster ki brain lantiది. It makes all the global decisions about the cluster (e.g., scheduling) and also detects and responds to cluster events. Ee components anni ayna **Master Node** lo run avthayi.

1.  **`kube-apiserver`**:
    -   Idi Control Plane ki main entry point. Think of it as the **Front Desk or Receptionist** of our company.
    -   Manam `kubectl` use chesi commands isthe, avi first ee API server ke velthayi.
    -   It exposes the **Kubernetes API**. Migatha components anni deenithone matladathayi. (Manam deeni gurinchi Chapter 12 lo inka detail ga chusam!)
    -   Vere components direct ga `etcd` tho matladavu, only API server matrame matladuthundi. Security! 🫡

2.  **`etcd`**:
    -   Idi mana cluster ki **Database**. Specifically, it's a consistent and highly-available key-value store.
    -   Mana cluster data antha (e.g., enni pods unnayi, evi ఏ node lo unnayi, etc.) ikkade store avthundi.
    -   Chala important component. Idi lekapothe, mana cluster state antha poyinatte. Anduke deeniki eppudu backup undali. 🙏

3.  **`kube-scheduler`**:
    -   Idi mana company lo **HR Recruiter** lanti వాడు.
    -   New ga create ayina **Pods** ki (employees ki) ye **Node** (desk) assign cheyalo decide chestundi. (Pods and Nodes gurinchi manam `Workloads` and `Cluster Architecture` sections lo nerchukuntam).
    -   It watches for newly created Pods that have no assigned node, and selects a node for them to run on based on resource requirements, policies, etc.

4.  **`kube-controller-manager`**:
    -   Idi mana company lo unna **different department managers** (like HR Manager, Finance Manager) kalipina oka component.
    -   It runs **controllers**, which are background threads that handle routine tasks in the cluster. (Manam `Controllers` gurinchi `Cluster Architecture` section lo detail ga chuddam).
    -   Logically, each controller is a separate process, but to reduce complexity, they are all compiled into a single binary and run in a single process.
    -   Konni examples: Node Controller, Job Controller, EndpointSlice Controller, ServiceAccount Controller.

5.  **`cloud-controller-manager`** (Optional):
    -   Idi manam AWS, GCP, Azure lanti cloud platforms meeda K8s run chesthe matrame untundi.
    -   It allows you to link your cluster into your cloud provider's API.
    -   Deeni valla Kubernetes core code, cloud-specific code nunchi separate ga untundi.

## B. Node Components (The Employees/Hands 🙌)

Ee components prathi **worker node** lo run avthayi. **Pods** ni run chesi, Kubernetes runtime environment ni provide chestayi.

1.  **`kubelet`**:
    -   Idi prathi node lo unde **Agent or Supervisor**.
    -   Control Plane nunchi vache commands ni idi receive cheskuni, aa node lo unna containers (Pods) sarigga run avthunnayo ledo chustundi.
    -   Kubelet lekunda node asalu cluster lo part avvaledu. It's the main link between the node and the control plane.

2.  **`kube-proxy`**:
    -   Idi prathi node lo unde **Network Magician**.
    -   It maintains network rules on nodes. Ee network rules valla, mana pods network communication (inside or outside the cluster) cheyagalugutayi.
    -   Basically, Kubernetes **Services** concept ni implement cheyadaniki idi help chestundi. (Manam `Services` gurinchi networking section lo chala detail ga nerchukuntam!).

3.  **`Container Runtime`**:
    -   Idi asalu container ni run chese software. The **Engine** of the car.
    -   Kubernetes supports many container runtimes like `containerd`, `CRI-O`, and any other that implements the Kubernetes CRI (Container Runtime Interface).
    -   Docker use cheyadam chala mandi ki alavatu, kani `dockershim` (v1.24 lo remove chesaru) lekunda Docker direct ga K8s tho work cheyadu. Ippudu `containerd` chala popular.

## C. Addons

Addons anevi Kubernetes cluster functionality ni extend chese pods and services.
*   **DNS:** Cluster-wide DNS service. CoreDNS anedi popular option.
*   **Web UI (Dashboard):** Cluster ni manage cheyadaniki oka graphical interface.
*   **Container Resource Monitoring:** Prometheus lanti tools tho container metrics collect cheyadaniki.
*   **Cluster-level Logging:** Logs anni oka central place lo save cheyadaniki.

> **🧠 Key Takeaway:** The **Control Plane** is the brain that makes decisions. The **Worker Nodes** are the hands that do the actual work of running our containers. The `kube-apiserver` is the only way they all talk to each other.

---

### Cliffhanger 🧗:

Phew! Mana K8s computer loni parts anni chusesam. Control Plane (brain) and Worker Nodes (hands) ela kalisi pani chestayo ardhamaindi kada?

Kani manam "Pod", "Service", "Object" lanti kotha padalu chala vinnam. Asalu **Objects in Kubernetes** ante enti? How do we tell Kubernetes what to do? How do we define the "desired state" we talked about?

In our next chapter, we will uncover the soul of Kubernetes: **Objects**! We'll learn how to speak the language of Kubernetes using YAML. Get ready, this is where the real development work begins! 🤗💻

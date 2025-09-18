# 📦 Chapter 3: Kubernetes Objects - The Soul of K8s 📦

Welcome back, Champion! Mana last session lo K8s loni parts (components) gurinchi matladukunnam. Ippudu manam K8s tho ela matladalo nerchukundam. How do we tell Kubernetes what we want? The answer is **Objects**!

Ee topic konchem theoretical anipinchina, idi chala chala important. Idi ardham aithe, K8s meku chala easy aipothundi. Just a little focus! 💪

## 1. What are Kubernetes Objects?

Simple ga cheppalante, **Kubernetes Objects anevi mana cluster yokka state ni represent chese entities.**

-   Avi **persistent entities**, ante manam create cheste, avi cluster lo untayi.
-   Avi **"records of intent"** (మన ఉద్దేశ్యం యొక్క రికార్డులు). Ante, "Naaku ee application 3 copies tho run avvali" ani manam chepthe, aa "intent" ni K8s oka object la save cheskuntundi.
-   Kubernetes system eppudu ee object existence ni ensure cheyadaniki try chestune untundi.

## 2. The Golden Duo: `spec` vs `status`

Prathi Kubernetes object lo ee rendu fields chala mukhyam. Interview lo pakka adugutaru! 🫡

-   **`spec` (Specification):** Idi manam define chestham. It's our **desired state**. "Naaku cluster ela undali anukuntunnanu" anedi ee field lo cheptam. (e.g., `replicas: 3`).
-   **`status`:** Idi Kubernetes system update chestundi. It's the **current state** of the object. Mana desired state ki, current state ki match avthunda leda anedi ikkada thelustundi.

Ee diagram chudandi, ee concept chala clear ga ardham avthundi.

```mermaid
graph LR
    subgraph You (The Developer)
        A[YAML Manifest 📄]
    end

    subgraph Kubernetes Control Plane (The Brain 🧠)
        B(API Server)
        C{Controller}
    end

    subgraph Cluster State
        D[etcd - Database]
        E[Worker Nodes]
    end

    A -- "1. kubectl apply" --> B;
    B -- "2. Stores Desired State (spec)" --> D;
    C -- "3. Watches for changes" --> B;
    C -- "4. Makes changes to match spec" --> E;
    E -- "5. Reports back Current State (status)" --> C;
    C -- "6. Updates status in etcd via API" --> B;

    style A fill:#f9f,stroke:#333,stroke-width:2px
```

**Flow antha ide:**
1.  Manam oka YAML file lo `spec` (desired state) define chesi `kubectl apply` kodatham.
2.  Aa request API Server ki velthundi, adi `etcd` lo save chestundi.
3.  Controller (from `kube-controller-manager`) ee change ni chusi, "Aha! User ki 3 replicas kavali, kani ippudu 0 unnai" ani anukuntundi.
4.  Controller Worker Nodes lo 3 new Pods create cheyadaniki pani start chestundi.
5.  Worker nodes lo Pods create ayyaka, aa information (current state) malli Control Plane ki velthundi.
6.  Controller aa `status` field ni update chestundi. Ippudu `spec.replicas` is 3 and `status.replicas` is also 3. Mission accomplished! 🔥

Okavela oka pod crash aithe, `status.replicas` 2 avthundi. Controller ee difference ni chusi, ventane inko pod ni start chestundi to match the `spec`. This is called **self-healing**.

## 3. Describing an Object: The YAML Manifest

Manam K8s ki mana "intent" cheppadaniki YAML files (manifests) vadatham. Ikkade mana developer skills chupiyali!

Prathi K8s object manifest lo ee 4 fields **required**:

1.  **`apiVersion`**: Ee object create cheyadaniki manam ye K8s API version vaduthunnamo cheppali. (e.g., `apps/v1`, `v1`).
2.  **`kind`**: Manam ye type of object create chestunnamo cheppali. (e.g., `Deployment`, `Pod`, `Service`).
3.  **`metadata`**: Ee object ni identify cheyadaniki information. Minimum `name` undali.
4.  **`spec`**: Idi mana desired state. Prathi `kind` ki `spec` format veru ga untundi.

### Example: Our First Deployment YAML

Let's look at a real example from the docs. Ee file ni `deployment.yaml` ani save cheskundam.

```yaml
# Ee line cheptundi, manam apps API group lo v1 version vaduthunnam ani.
apiVersion: apps/v1
# Manam create chese object 'Deployment' ani cheptunnam.
kind: Deployment
# Ee object gurinchi extra information.
metadata:
  # Ee deployment peru 'nginx-deployment'. Idi unique ga undali within a namespace.
  name: nginx-deployment
# Idi asalu magic. Mana desired state antha ikkade untundi.
spec:
  # Ee deployment enni pods create cheyalo cheptundi.
  replicas: 2
  # Ee deployment ye pods ni manage cheyalo cheppadaniki selector use avthundi.
  selector:
    # 'matchLabels' ante, ee labels unna pods ni this deployment owns.
    matchLabels:
      app: nginx
  # Pods ela undalo cheppe template idi.
  template:
    # Pod ki metadata.
    metadata:
      # Pods ki labels isthunnam. Ee label selector tho match avvali.
      labels:
        app: nginx
    # Pod yokka actual specification.
    spec:
      # Ee pod lo em containers undalo cheptunnam.
      containers:
      # Container ki peru.
      - name: nginx
        # Ee container run cheyadaniki ye Docker image kavalo cheptunnam.
        image: nginx:1.14.2
        # Ee container ye ports expose chestundo cheptunnam.
        ports:
        - containerPort: 80
```

Ee file ni cluster ki apply cheyadaniki ee command vadatham:
`kubectl apply -f deployment.yaml`

Appudu Kubernetes ee file ni chusi, 2 Nginx pods ni create chesi, cluster state ni mana desired state ki match chestundi. Simple! 🤗

---

### Cliffhanger 🧗:

Okay, manam ippudu K8s tho ela matladalo nerchukunnam (YAML tho!). Kani ee `metadata` lo unna `name`, `labels` gurinchi inkonchem detail ga theluskovali. How does Kubernetes use these names and labels to organize everything? Can we have objects with the same name?

In the next chapter, we will become masters of organization by learning all about **Object Names, IDs, Labels, and Selectors**. Ee concepts tho manam cluster ni chala neat ga manage cheyochu. Ready to become a K8s librarian? 🤓📚

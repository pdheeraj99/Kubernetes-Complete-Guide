# 📦 Chapter 3: Kubernetes Objects - The Soul of K8s 📦

Welcome back, Champion! Mana last session lo K8s loni parts (components) gurinchi matladukunnam. Ippudu manam K8s tho ela matladalo nerchukundam. How do we tell Kubernetes what we want? The answer is **Objects**!

Ee topic konchem theoretical anipinchina, idi chala chala important. Idi ardham aithe, K8s meku chala easy aipothundi. Just a little focus! 💪

**What we will learn in this chapter:**
-   What is a Kubernetes Object?
-   The most important concept: `spec` (what you want) vs. `status` (what you have).
-   How to describe an object using a YAML manifest.

## 1. What are Kubernetes Objects?

Simple ga cheppalante, **Kubernetes Objects anevi mana cluster yokka state ni represent chese entities.**

-   Avi **persistent entities**, ante manam create cheste, avi cluster lo untayi.
-   Avi **"records of intent"** (మన ఉద్దేశ్యం యొక్క రికార్డులు). Ante, "Naaku ee application 3 copies tho run avvali" ani manam chepthe, aa "intent" ni K8s oka object la save cheskuntundi.
-   The Kubernetes system, through its **Controllers** (which we learned about in Chapter 2), constantly works to ensure this object's desired state becomes a reality.

## 2. The Golden Duo: `spec` vs `status`

Prathi Kubernetes object lo ee rendu fields chala mukhyam. Interview lo pakka adugutaru! 🫡

-   **`spec` (Specification):** Idi manam define chestham. It's our **desired state**.
-   **`status`:** Idi Kubernetes system update chestundi. It's the **current state** of the object.

**The Thermostat Analogy 🌡️:**
-   Think of `spec` as the temperature you **set** on your AC's thermostat. For example, `spec: { temperature: 24°C }`.
-   Think of `status` as the **current** temperature in the room, which the thermostat's sensor reads. For example, `status: { currentTemperature: 28°C }`.
-   The AC unit itself is the **Controller**. It sees the difference and works hard to cool the room until `status` matches the `spec`.

Ee diagram chudandi, K8s lo ee concept ela pani chestundo chala clear ga ardham avthundi.

```mermaid
graph LR
    subgraph You (The Developer)
        A[YAML Manifest 📄<br/><b>spec: { replicas: 3 }</b>]
    end

    subgraph Kubernetes Control Plane (The Brain 🧠)
        B["API Server (Chapter 12)"]
        C["Controller (Chapter 2)"]
    end

    subgraph Cluster State
        D["etcd (Database)<br/>Desired State Stored"]
        E["Worker Nodes<br/>Current State: 1 Pod Running"]
    end

    A -- "1. kubectl apply" --> B;
    B -- "2. Stores Desired State (spec)" --> D;
    C -- "3. Watches for changes" --> B;
    C -- "4. Makes changes to match spec<br/>(Creates 2 more Pods)" --> E;
    E -- "5. Reports back Current State<br/>(status: 3 Pods Running)" --> C;
    C -- "6. Updates status in etcd via API" --> B;

    style A fill:#f9f,stroke:#333,stroke-width:2px
```

**Flow antha ide:**
1.  Manam oka YAML file lo `spec` (desired state) define chesi `kubectl apply` kodatham.
2.  Aa request **API Server** ki velthundi, adi `etcd` lo save chestundi.
3.  The **Controller** ee change ni chusi, "Aha! User ki 3 replicas kavali, kani ippudu 1 undi" ani anukuntundi.
4.  Controller Worker Nodes lo inko 2 new Pods create cheyadaniki pani start chestundi.
5.  Worker nodes lo Pods create ayyaka, aa information (current state) malli Control Plane ki velthundi.
6.  Controller aa `status` field ni update chestundi. Ippudu `spec.replicas` is 3 and `status.replicas` is also 3. Mission accomplished! 🔥

Okavela oka pod crash aithe, `status.replicas` 2 avthundi. Controller ee difference ni chusi, ventane inko pod ni start chestundi to match the `spec`. This is called **self-healing**.

## 3. Describing an Object: The YAML Manifest

Manam K8s ki mana "intent" cheppadaniki YAML files (manifests) vadatham. Ikkade mana developer skills chupiyali!

Prathi K8s object manifest lo ee 4 fields **required**:

1.  **`apiVersion`**: Ee object create cheyadaniki manam ye K8s API version vaduthunnamo cheppali. (Manam deeni gurinchi **The Kubernetes API** chapter lo detail ga chusam).
2.  **`kind`**: Manam ye type of object create chestunnamo cheppali. (e.g., `Deployment`, `Pod`, `Service`).
3.  **`metadata`**: Ee object ni identify cheyadaniki information. Minimum `name` undali. (Deeni gurinchi `Names`, `Labels`, `Annotations` chapters lo inka nerchukuntam).
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

> **🧠 Key Takeaway:** Manam Kubernetes tho "Do this, then do that" ani cheppamu (imperative). Manam just "I want this final result" ani cheptam (declarative). How to get there is Kubernetes's headache. That's the core soul of K8s!

---

### Cliffhanger 🧗:

Okay, manam ippudu K8s tho ela matladalo nerchukunnam (YAML tho!). Kani ee `metadata` lo unna `name`, `labels` gurinchi inkonchem detail ga theluskovali. How does Kubernetes use these names and labels to organize everything? Can we have objects with the same name?

In our next chapter, we will become masters of organization by learning all about **Object Names, IDs, Labels, and Selectors**. Ee concepts tho manam cluster ni chala neat ga manage cheyochu. Ready to become a K8s librarian? 🤓📚

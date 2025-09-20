# 🗺️ Chapter 7: Namespaces - Divide and Conquer! 🗺️

Welcome back, Champion! So far, manam objects anni create chesam, kani antha oke chota (in the `default` namespace) chestunnam. Real-world projects lo, multiple teams and multiple environments (dev, qa, prod) untayi. Anni oke chota unte, chala godava avthundi! Oka team member, inko team di accidentally delete cheste? Disaster! 😂

For this, Kubernetes gives us a powerful feature for isolation: **Namespaces**.

**What we will learn in this chapter:**
-   What Namespaces are and why they are like "virtual clusters".
-   The default Namespaces that come with every K8s cluster.
-   How to create and manage objects within a Namespace.
-   How Namespaces affect DNS inside the cluster.

## 1. What are Namespaces?

-   Namespaces are a way to create multiple **virtual clusters** inside a single physical Kubernetes cluster.
-   **Analogy:** Think of your physical cluster as a big apartment building (K8s Heights). A namespace is like your own personal flat (e.g., `Flat-101`, `Flat-102`).
-   Inside your flat, you can name your rooms whatever you want (`living-room`, `kitchen`). Your neighbor in `Flat-102` can also have rooms with the exact same names. No problem! Your `kitchen` is separate from their `kitchen`.
-   They provide a **scope for names**. As we learned in **Chapter 4 (Names and UIDs)**, object names only need to be unique *within* their namespace.

```mermaid
graph TD
    subgraph "Kubernetes Cluster (Apartment Building)"
        direction LR
        subgraph "Namespace: team-a (Flat 101)"
            P1["Pod: web-server"]
            S1["Service: database"]
        end
        subgraph "Namespace: team-b (Flat 102)"
            P2["Pod: web-server"]
            S2["Service: database"]
        end
    end

    style "team-a" fill:#d4edda,stroke:#155724,stroke-width:2px
    style "team-b" fill:#f8d7da,stroke:#721c24,stroke-width:2px
```
*In this diagram, `web-server` Pod exists in both `team-a` and `team-b` namespaces without any conflict.*

## 2. When to Use Namespaces?

-   **Multi-tenancy:** When multiple teams or projects share the same cluster.
-   **Resource Isolation:** To prevent teams from interfering with each other. For example, the `dev` team shouldn't be able to accidentally delete a `prod` service.
-   **Resource Management:** To divide cluster resources using **ResourceQuotas**. For example, you can say the `dev` namespace can only use 10 CPUs and 20GB of RAM. (We will learn about ResourceQuotas in the `Policies` section!).

## 3. The Initial Namespaces

When you start a Kubernetes cluster, it already has 4 namespaces:
1.  **`default`**: Manam namespace specify cheyakapothe, anni objects ikkade create avthayi. Real projects lo idi vadakudadu, it's like leaving your stuff in the apartment lobby!
2.  **`kube-system`**: Kubernetes system components (like `etcd`, `kube-apiserver` from **Chapter 2**) ikkada untayi. Ee namespace ni eppudu, eppudu touch cheyoddu! 🙏
3.  **`kube-public`**: Ee namespace ni andaru (even unauthenticated users) read cheyochu. Mostly cluster info kosam.
4.  **`kube-node-lease`**: Prathi node ki oka `Lease` object untundi, node health check (heartbeats) kosam.

## 4. Working with Namespaces

### Viewing Namespaces
Cluster lo unna anni namespaces chudalante:
```bash
kubectl get namespace
# or the super-fast shortcut
kubectl get ns
```

### Creating a Namespace
Manam oka YAML file tho namespace create cheyochu. Let's see `namespace-dev.yaml`:

```yaml
# API version for namespace is v1
apiVersion: v1
# The kind of object is Namespace
kind: Namespace
# Metadata section lo, manam namespace ki peru istham
metadata:
  name: dev
```
**Command to apply:** `kubectl apply -f namespace-dev.yaml`

Alternatively, you can use a simple command: `kubectl create namespace dev`

### Creating Objects in a Namespace
Oka object ni specific namespace lo create cheyadaniki, manam `metadata` lo `namespace: dev` ani add cheyochu, or command line lo `--namespace` flag (short `-n`) vadali.

**1. Using the `--namespace` flag:**
```bash
# Creates an nginx pod in the 'dev' namespace
kubectl run nginx --image=nginx -n dev

# To see pods in that namespace
kubectl get pods -n dev
```

**2. Setting the namespace preference for your terminal:**
Eppudu `-n dev` ani type cheyadam kashtam anipisthe, you can set it as a default for your current terminal session.
```bash
# This command only changes your local kubectl settings, not the cluster!
kubectl config set-context --current --namespace=dev
```
Ippati nunchi, `kubectl get pods` ante, adi automatic ga `dev` namespace lo chustundi.

## 5. Namespaces and DNS

This is a super important point for developers!
-   Oka **Service** create chesinappudu, K8s oka DNS entry create chestundi.
-   The format is: `<service-name>.<namespace-name>.svc.cluster.local`
-   **Example:** `my-db` ane service `dev` namespace lo unte, daani full DNS name: `my-db.dev.svc.cluster.local`.
-   **Advantage:** If your application is also inside the `dev` namespace, you can just use the short name `my-db` to connect to the service. If you are in another namespace (like `prod`) and want to connect to the `dev` database, you must use the full name `my-db.dev`.

> **🧠 Key Takeaway:** Namespaces are the primary tool for isolation in Kubernetes. They scope names, access control (RBAC), and resources (Quotas), allowing multiple teams and environments to coexist safely in one cluster.

---

### Cliffhanger 🧗:

Okay, we've learned how to organize our objects into different rooms (Namespaces). But what if we want to find objects based on their properties, not just their labels? For example, "Show me all pods that are in the 'Running' state" or "Show me all pods that are on 'node-1'".

Labels can't do this because they are static metadata. For this kind of dynamic, status-based filtering, Kubernetes provides another powerful tool.

In our next chapter, we will explore **Field Selectors**! Get ready to query your objects like a database pro! 🔎

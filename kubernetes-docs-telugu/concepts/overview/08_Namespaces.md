# 🏢 Chapter 8: Namespaces - Dividing Your Cluster

Namaste Mawa! So far, so good. Manam ippudu objects ni ela create cheyalo, identify cheyalo, group cheyalo nerchukunnam. But, ippativaraku manam pani chesindantha `default` ane oka pedda open office space lo.

Imagine mee company lo multiple teams (Dev, QA, Production) unnai. Andaru oke open space lo pani chestunte, godavalu, confusion common. Team A `database` anedi Team B `database` tho conflict avvochu.

Ee problem ni solve cheyadaniki, Kubernetes manaki **Namespaces** ane concept ichindi.

## The Analogy: Office Building & Floors 🏢

*   **Kubernetes Cluster:** Idi oka pedda multi-story office building.
*   **Namespaces:** Ee building lo prathi floor oka **Namespace**.
    *   `dev-floor` (Development Team)
    *   `qa-floor` (QA Team)
    *   `prod-floor` (Production Team)
*   **Resources (Pods, Services):** Ee floors lo unna cabins, meeting rooms lanti vi.

Dev team valla `dev-floor` lo `meeting-room-1` ani pettukovachu. Ade peru tho QA team kuda valla `qa-floor` lo `meeting-room-1` ani pettukovachu. No problem! The floor (Namespace) provides the **scope** and **isolation**.

```mermaid
graph TD
    subgraph Cluster (Office Building)
        subgraph ns-dev [Namespace: dev]
            pod-a-dev["Pod A (name: my-app)"]
            svc-a-dev["Service A (name: my-service)"]
        end
        subgraph ns-prod [Namespace: prod]
            pod-a-prod["Pod B (name: my-app)"]
            svc-a-prod["Service B (name: my-service)"]
        end
    end
    style ns-dev fill:#cce5ff,stroke:#333
    style ns-prod fill:#d4edda,stroke:#333
```
*Notice: Pod peru `my-app` rendu namespaces lo undi, but no conflict!*

---

## Why Use Namespaces?

1.  **Scope for Names:** To avoid name collisions between teams/projects. (As seen above).
2.  **Resource Quotas:** Prathi team ki (namespace ki) resource limits pettadaniki. "Dev team, meeru 10 CPU and 20GB RAM కంటే ekkuva vaadakudadu" ani cheppochu.
3.  **Access Control (Authorization):** "Dev team members ki kevalam `dev` namespace lo ne access undali, `prod` ni chudataniki kuda permission ledu" ani rules pettadaniki.

---

## The Default Namespaces

Meeru `kubectl get namespaces` ani kodithe, meeku already konni kanipistayi. Veetini Kubernetes manaకోసం create chestundi. Don't touch them unless you know what you're doing! 😂

| Namespace         | Purpose                                                              |
| ----------------- | -------------------------------------------------------------------- |
| `default`         | The "open office" space. Manam namespace specify cheyakapothe, anni ikkadike vastayi. Production lo idi vaadakudadu! |
| `kube-system`     | **Very Important!** Kubernetes Control Plane components (API Server, Scheduler etc.) ikkade untayi. Ee namespace ni gelikithe, cluster dobbinatte! 🙏 |
| `kube-public`     | Cluster antha public ga kanapadalsina resources ikkada pedataru. Very rare to use. |
| `kube-node-lease` | Prathi node yokka health status (heartbeats) ni manage chese `Lease` objects ikkada untayi. |

---

## Working with Namespaces (kubectl Commands)

*   **List all namespaces:**
    ```bash
    kubectl get namespaces
    ```

*   **Create a new namespace:**
    ```bash
    kubectl create namespace dev
    ```

*   **Get pods from a specific namespace:**
    ```bash
    # Long version
    kubectl get pods --namespace=dev
    # Short version (pro-tip!)
    kubectl get pods -n dev
    ```

*   **Set a default namespace for your current context (Super useful!):**
    Ika meeda prathi command ki `-n dev` ani ivvakunda, default set cheskovachu.
    ```bash
    kubectl config set-context --current --namespace=dev
    # Now, if you run 'kubectl get pods', it will only show pods from 'dev'
    ```

---

## Namespaces and DNS

This is a key concept for microservices.
*   Oka namespace lo unna pod, inko namespace lo unna service ni ela pilustundi? Through a special DNS address.
*   The format is: `<service-name>.<namespace-name>.svc.cluster.local`
*   **Example:** `payment-service` ane service `prod` namespace lo undi. `frontend` namespace lo unna pod daanni `payment-service.prod` ani pilavochu.
*   If they are in the same namespace, just `<service-name>` is enough.

---

## Important: Not All Objects are in a Namespace!

Konni objects cluster antha common ga untayi, vatiki virtual walls undavu. They are **cluster-scoped**.

*   **Examples:** `Node`, `PersistentVolume`, `StorageClass`, and `Namespace` itself.
*   **Why?** A Node (physical/virtual machine) belongs to the whole cluster, not just one team. Simple logic!

*   **How to check which resources are namespaced?**
    ```bash
    # To see all namespaced resources
    kubectl api-resources --namespaced=true

    # To see all cluster-scoped (non-namespaced) resources
    kubectl api-resources --namespaced=false
    ```

**Next Enti? (CLIFFHANGER! 🎬)**

We've learned how to create objects, and now how to isolate them in namespaces. But what happens when we delete an object? For example, if we delete a `Deployment`, what happens to the `Pods` it created? How does Kubernetes know to clean them up? This parent-child relationship is managed through a concept called **Owners and Dependents**, and the cleanup process is handled by **Finalizers**. Let's explore this lifecycle magic next! ✨

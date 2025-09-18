# 📛 Chapter 4: Object Names and UIDs - Peru Peru lo Emi Undi? 📛

Welcome back, Champion! Last chapter lo manam K8s objects gurinchi nerchukunnam. Ippudu, aa objects ki perlu ela pettalo chuddam. Just like how we have names, our Kubernetes objects also have names and even a unique ID card!

Ee topic simple ga anipinchina, cluster ni organized ga unchukovadaniki chala important.

## 1. The Two Identifiers: Name vs. UID

Kubernetes lo prathi object ki rendu identifiers untayi:
1.  **Name:** Manam iche peru (client-provided).
2.  **UID (Unique ID):** Kubernetes system iche peru (system-generated).

### 👉 Name

-   Idi manam object create chesetappudu `metadata` section lo istham. (e.g., `name: my-first-pod`).
-   **Uniqueness Rule:** Oka `namespace` lo, oka `kind` (type) of object ki, okate peru undali.
    -   Ante, `default` namespace lo `my-pod` ane peru tho okate Pod undagaladu.
    -   Kani, `default` namespace lo `my-pod` ane peru tho oka Pod and `my-pod` ane peru tho oka Service undochu. Kind veru kabatti, problem ledu.
    -   Also, `default` namespace lo `my-pod` ane Pod, and `test` namespace lo `my-pod` ane Pod undochu. Namespace veru kabatti, problem ledu.

```mermaid
graph TD
    subgraph Namespace: default
        P1["Pod: my-app"]
        S1["Service: my-app"]
    end

    subgraph Namespace: test
        P2["Pod: my-app"]
    end

    subgraph Namespace: production
        P3["Pod: my-app"]
    end

    style P1 fill:#d4edda
    style S1 fill:#d4edda
    style P2 fill:#f8d7da
    style P3 fill:#cce5ff
```
*Diagram lo chudandi, 'my-app' ane peru tho objects different kinds or different namespaces lo undochu.*

-   If you delete an object, you can create a new object with the same name.

#### Naming Conventions (Perlu Ela Pettali?)

Perlu pettadaniki konni rules unnayi. Most resources require names that can be used as **DNS Subdomain Names (RFC 1123)**.
-   Max 253 characters.
-   Only lowercase alphanumeric characters, '-', or '.'
-   Must start and end with an alphanumeric character.
-   **Example:** `my-app-v1.backend-service` (Valid)
-   **Example:** `_my-app` or `my-app-` (Invalid)

### 👉 UID (Unique ID)

-   Idi Kubernetes system generate chestundi. Manam ivvamu.
-   It is **unique across the entire cluster for all time**.
-   Analogy: Mana `Name` laంటిది K8s object `Name`. Mana `Aadhaar Card Number` లాంటిది K8s object `UID`. Peru maarochu (object delete chesi create cheste), kani Aadhaar number la UID eppatiki unique ga untundi.
-   Even if you delete an object and create a new one with the same name, the new object will have a completely different UID.
-   This helps Kubernetes distinguish between an object that was deleted and a new one that just happens to have the same name. It's crucial for reliability.

## Summary Table

| Feature        | Name                               | UID                                       |
|----------------|------------------------------------|-------------------------------------------|
| **Who provides?** | You (the user/client)              | Kubernetes System                         |
| **Uniqueness** | Unique within a namespace & kind   | Unique across the entire cluster, forever |
| **Reusability**  | Yes, after deleting the old object | No, never reused                          |
| **Analogy**      | Your Name                          | Your Aadhaar/SSN Number                   |

---

### Cliffhanger 🧗:

Okay, ippudu mana objects ki perlu ela pettalo, K8s వాటిని uniquely ela track chestundo telisindi. Kani peru okate saripodu. What if we want to organize objects in a more flexible way? For example, "Show me all the applications that belong to the 'frontend' team" or "Show me all the 'production' databases".

Names can't do this. For this, we need something more powerful. We need... **Labels and Selectors**!

In our next chapter, we will learn how to stick labels on our objects and then use selectors to filter and find them with precision. It's like using hashtags for your Kubernetes resources! Get ready to organize! 🏷️🔍

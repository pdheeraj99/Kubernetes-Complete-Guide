# 🧑‍🔬 K8s API - Practical Examples & Lab Work 🧑‍🔬

Mawa, mana `notes.md` lo API gurinchi theory antha chusam. Ippudu konni practical examples chuddam, so the concept gets permanently stamped in our brain!

## 1. Mapping `kind` and `apiVersion` to Real API Paths

Manam YAML lo `kind: Pod` and `apiVersion: v1` ani rasthunnam. Behind the scenes, `kubectl` daanini ee kindha cheppina API path la convert chestundi.

Here are some common resources and their actual REST API endpoints. Notice the pattern of `/api/v1` for the core group and `/apis/GROUP/VERSION` for others.

| Resource Kind         | `apiVersion`                | Example HTTP GET Request Path                               |
| --------------------- | --------------------------- | ----------------------------------------------------------- |
| **Pod**               | `v1`                        | `/api/v1/namespaces/{namespace}/pods/{pod-name}`            |
| **Service**           | `v1`                        | `/api/v1/namespaces/{namespace}/services/{service-name}`      |
| **ConfigMap**         | `v1`                        | `/api/v1/namespaces/{namespace}/configmaps/{configmap-name}`  |
| **Deployment**        | `apps/v1`                   | `/apis/apps/v1/namespaces/{namespace}/deployments/{dep-name}` |
| **ReplicaSet**        | `apps/v1`                   | `/apis/apps/v1/namespaces/{namespace}/replicasets/{rs-name}`  |
| **Job**               | `batch/v1`                  | `/apis/batch/v1/namespaces/{namespace}/jobs/{job-name}`       |
| **Role**              | `rbac.authorization.k8s.io/v1` | `/apis/rbac.authorization.k8s.io/v1/namespaces/{namespace}/roles/{role-name}` |
| **ClusterRole**       | `rbac.authorization.k8s.io/v1` | `/apis/rbac.authorization.k8s.io/v1/clusterroles/{role-name}` |

*Note: `{namespace}` and `{resource-name}` are placeholders.*

## 2. See The Magic Live! Unmasking `kubectl` 🎭

Manam ippativaraku `kubectl` anedi just oka messenger boy ani anukunnam kada. Nuvvu adhe chese REST calls ni live ga chudochu!

Try this command in your terminal:

```bash
# The -v=6 flag increases the verbosity to level 6
# At this level, kubectl prints the raw HTTP requests it's making!
kubectl get pods -v=6
```

Ee command run chesinappudu, neeku chala output vasthundi. Aa output lo, ee kindha unna lines lanti lines kosam chudu:

```
I0519 10:00:00.123456   12345 round_trippers.go:463] GET https://<your-cluster-ip>/api/v1/namespaces/default/pods
I0519 10:00:00.123456   12345 round_trippers.go:470] Request Headers:
I0519 10:00:00.123456   12345 round_trippers.go:474]     Accept: application/json;as=Table;v=v1;g=meta.k8s.io, application/json
...
I0519 10:00:00.234567   12345 round_trippers.go:529] Response Status: 200 OK ...
```

Chusava! `kubectl get pods` anedi nijanga `GET https://.../api/v1/namespaces/default/pods` ane oka REST call eh.

You can try this with any `kubectl` command (`get`, `describe`, `delete`, etc.) with the `-v=6` (or higher) flag to see what's happening behind the scenes. This is a super powerful debugging technique! 🔥

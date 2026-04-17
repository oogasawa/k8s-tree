# k8s-tree

Visualize Kubernetes cluster state as a tree in your terminal.

```
├── ns: sc-account
│   ├── deploy/
│   │   ├── sc-account-unit1 (0/0)
│   │   └── sc-account-unit2 (1/1)
│   ├── gw: sc-account-gateway
│   ├── route/
│   │   ├── sc-account-route  [B/G: unit1=0, unit2=100 ★]
│   │   └── sc-auth-route     [B/G: unit1=0, unit2=100 ★]
│   ├── svc/
│   │   ├── keycloak
│   │   ├── sc-account-unit1
│   │   └── sc-account-unit2
│   └── pvc/
│       ├── postgres-unit1-pvc
│       └── postgres-unit2-pvc
└── ns: k8s-pups
    ├── deploy: k8s-pups (1/1)
    └── svc: k8s-pups
```

## Features

- **Tree layout** — namespaces, Deployments, StatefulSets, Services, PVCs, Gateways, and HTTPRoutes in one view
- **Replica status** — `(ready/desired)` shown next to each Deployment and StatefulSet
- **Blue/Green detection** — HTTPRoutes with multiple `backendRefs` show weights automatically; the active side (weight=100) is marked with ★
- **Zero dependencies** — single `.java` file, JDK 11+, no build step required
- **Flexible input** — auto-invokes `kubectl` when run interactively; reads piped JSON otherwise

## Requirements

- JDK 11 or later
- `kubectl` configured and pointing at your cluster (for auto-invocation mode)

## Usage

```bash
# Show all namespaces (auto-invokes kubectl)
java K8sTree.java

# Show a specific namespace
java K8sTree.java -n sc-account

# Hide infrastructure namespaces (kube-system, longhorn-system, etc.)
java K8sTree.java --no-infra

# List namespace names only
java K8sTree.java --ns
java K8sTree.java --ns --no-infra

# Pipe kubectl output directly
kubectl get deploy,sts,svc,pvc,httproute,gateway -A -o json | java K8sTree.java
```

## Resource types displayed

| Label | Kind |
|-------|------|
| `deploy` | Deployment |
| `sts` | StatefulSet |
| `svc` | Service |
| `pvc` | PersistentVolumeClaim |
| `gw` | Gateway (Envoy Gateway API) |
| `route` | HTTPRoute |

## Blue/Green weight display

When an HTTPRoute has two or more `backendRefs`, k8s-tree treats it as a Blue/Green route and shows the weights inline:

```
route: sc-account-route  [B/G: unit1=0, unit2=100 ★]
```

The ★ marks the backend with weight=100 (the active side).

## Infrastructure namespaces

The following namespaces are hidden when `--no-infra` is specified:

`kube-system`, `kube-public`, `kube-node-lease`, `longhorn-system`, `envoy-gateway-system`, `ingress`

## License

MIT

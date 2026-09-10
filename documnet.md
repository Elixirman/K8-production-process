# Kubernetes Glossary — Plain-English Reference

Every term and service from the walkthrough, defined simply and correctly. Organized the same way as the animated series, so you can look something up right after seeing it.

---

## Core Concepts

| Term | What it actually means |
|---|---|
| **Cluster** | A set of machines (nodes) running Kubernetes together — some manage the cluster, others run your apps. |
| **Node** | One machine (physical or virtual) in the cluster. Either a control-plane node or a worker node. |
| **Pod** | The smallest deployable unit. One or more containers that share the same network and storage — usually just one container per Pod. |
| **YAML manifest** | A text file describing the *desired state* of something (e.g. "I want 3 replicas of this app running"). You apply it; Kubernetes makes it real. |
| **kubectl** | The command-line tool you use to talk to a cluster (`kubectl apply -f file.yaml`). |
| **Desired state / Reconciliation** | You declare what you *want*; Kubernetes continuously checks and corrects reality until it matches. This loop runs forever, not just once. |
| **Namespace** | A way to split one cluster into isolated "rooms" — for different teams, environments, or apps — so names don't collide and access can be restricted. |
| **Label / Selector** | Labels are tags you put on objects (`app: web-app`). Selectors are how other objects (like a Service) find things by those tags. |

---

## Part A · 01 — Control Plane (the brain)

| Term | Plain definition |
|---|---|
| **API Server** | The front door. Every command — yours, a controller's, a tool's — goes through it. It checks you're allowed to do the thing, then does it. |
| **etcd** | The cluster's memory. A database that stores the current and desired state of everything. If etcd is lost, the cluster's memory of itself is lost too. |
| **Scheduler** | The matchmaker. Looks at new Pods with no home yet and decides which node they should run on. |
| **Controller Manager** | The cluster's autopilot. Constantly checks "is reality matching what was asked for?" and fixes it if not. |
| **Admission Controller** | A checkpoint that runs right before something is saved to etcd — can reject or auto-correct a request (e.g. "no containers running as root allowed"). |
| **CoreDNS** | The cluster's phone book. Turns names like `my-service` into actual network addresses. |

## Part A · 02 — Worker Nodes & Workloads

| Term | Plain definition |
|---|---|
| **kubelet** | The agent on every worker node. Takes orders from the API Server and makes sure the right containers are actually running. |
| **Container runtime (CRI)** | The software that actually starts and stops containers — containerd or CRI-O. |
| **kube-proxy** | Handles the network plumbing so traffic sent to a Service reaches the right Pod. |
| **Deployment** | "Keep N copies of this stateless app running, and handle rolling updates for me." The most common workload type. |
| **ReplicaSet** | The thing a Deployment creates behind the scenes to actually keep the right number of Pods alive. You rarely touch this directly. |
| **StatefulSet** | Like a Deployment, but for apps that need a stable identity and their own storage — databases, message queues. |
| **DaemonSet** | "Run exactly one copy of this on every node" — used for things like log collectors or monitoring agents. |
| **Job** | Run a task to completion once, then stop. |
| **CronJob** | A Job that runs on a schedule, like a cron task. |
| **Taint / Toleration** | A taint on a node repels Pods ("stay off unless you tolerate me") — used to reserve nodes for special workloads (e.g. GPUs). |
| **Affinity / Anti-affinity** | Rules that say "put this Pod near/away from other Pods or nodes" — used to spread replicas across zones for real availability. |
| **PodDisruptionBudget (PDB)** | A guardrail that says "never take down more than X Pods at once," even during planned maintenance. |

## Part A · 03 — Networking, Ingress & Mesh

| Term | Plain definition |
|---|---|
| **CNI (Container Network Interface)** | The plugin that actually wires up pod-to-pod networking (Calico, Cilium, etc). Kubernetes needs one — it doesn't ship with networking built in. |
| **Service** | A stable name and address for a group of Pods, even as individual Pods come and go. |
| **ClusterIP / NodePort / LoadBalancer** | Three ways a Service can be exposed — internal-only, a port on every node, or an external cloud load balancer. |
| **Ingress / Gateway API** | Routes external web traffic (HTTP/HTTPS) into the right Service inside the cluster, based on hostname or path. |
| **cert-manager** | Automatically requests, renews, and installs TLS certificates so you don't do it by hand. |
| **Service Mesh** (Istio, Linkerd) | An extra layer that handles service-to-service traffic — encryption, retries, canary routing — at the cost of added complexity. |
| **mTLS** | Both sides of a connection prove their identity with certificates, not just the server. Used inside a service mesh. |

## Part A · 04 — Storage

| Term | Plain definition |
|---|---|
| **StorageClass** | A template that says *how* to create storage on demand (which type of disk, which provider). |
| **CSI Driver** | The plugin that actually talks to the cloud provider or storage system to create/attach disks. |
| **PersistentVolume (PV)** | An actual piece of storage that's been provisioned. |
| **PersistentVolumeClaim (PVC)** | A Pod's request for storage — "I need 10GB" — which gets matched to a PV. |
| **VolumeSnapshot** | A point-in-time copy of a PVC, used for backup or cloning. |
| **ConfigMap** | Stores non-secret configuration (settings, URLs, flags) that Pods can read. |
| **Secret** | Like a ConfigMap, but for sensitive values (passwords, tokens). Base64-encoded by default — **not encrypted** unless you set that up separately. |

## Part A · 05 — GitOps & CI/CD

| Term | Plain definition |
|---|---|
| **GitOps** | The idea that Git is the single source of truth for what should be running — a tool watches Git and makes the cluster match it. |
| **ArgoCD / Flux** | Tools that implement GitOps — they watch a Git repo and sync the cluster to match it automatically. |
| **Helm** | A package manager for Kubernetes — bundles a set of YAML files into a reusable, configurable "chart." |
| **Kustomize** | A way to customize plain YAML for different environments (dev/staging/prod) without duplicating files. |
| **Canary / Blue-Green deployment** | Ways to roll out a new version to a small slice of traffic first, instead of switching everyone over at once. |
| **Image scanning** | Automatically checking a container image for known vulnerabilities before it's allowed to run. |

## Part A · 06 — Autoscaling

| Term | Plain definition |
|---|---|
| **HPA (Horizontal Pod Autoscaler)** | Adds or removes Pod replicas based on CPU/memory or custom metrics. |
| **VPA (Vertical Pod Autoscaler)** | Adjusts how much CPU/memory a Pod *requests*, over time, instead of changing replica count. |
| **Cluster Autoscaler** | Adds or removes entire nodes when Pods can't be scheduled due to lack of capacity. |
| **KEDA** | Scales workloads based on events — queue length, message lag — not just CPU/memory. |

## Part A · 07 — Security & Governance

| Term | Plain definition |
|---|---|
| **RBAC (Role-Based Access Control)** | Controls who (or what service account) is allowed to do what, on which resources. |
| **NetworkPolicy** | A firewall rule for Pods — by default all Pods can talk to each other; NetworkPolicies restrict that. |
| **Pod Security Admission (PSA)** | Enforces baseline safety rules on Pods — e.g. don't run as root, don't allow privilege escalation. |
| **Vault / External Secrets Operator** | Tools that keep real secrets out of Kubernetes Secret objects and Git, pulling them in securely at runtime. |
| **cosign / Sigstore** | Tools for signing and verifying container images, so you know an image wasn't tampered with. |

## Part A · 08 — Observability

| Term | Plain definition |
|---|---|
| **Prometheus** | Collects and stores metrics (numbers over time) from the cluster and your apps. |
| **Grafana** | Turns those metrics into dashboards you can actually look at. |
| **Loki** | Collects and stores logs, designed to pair naturally with Prometheus/Grafana. |
| **Jaeger / Tempo** | Tracks a single request as it travels through multiple services — essential once you have more than one service talking to another. |
| **Alertmanager** | Turns "this metric crossed a threshold" into an actual page/notification to a human. |

## Part A · 09 — Day-2 Operations

| Term | Plain definition |
|---|---|
| **Velero** | Backs up and restores whole namespaces or clusters — not just etcd. |
| **etcd snapshot** | A backup of the cluster's entire memory — needed to recover from a lost control plane. |
| **Multi-cluster / Multi-region** | Running more than one cluster, often in different regions, so a full region outage doesn't take everything down. |
| **ResourceQuota** | Caps total CPU/memory/object count a namespace (usually = a team) can consume. |
| **LimitRange** | Sets default and max resource requests/limits per Pod within a namespace. |
| **Kubecost / OpenCost** | Tools that show what each namespace or team is actually costing you on a shared cluster. |

## Part B — The Five Core Processes (quick recap)

| Process | One-line version |
|---|---|
| **Deployment Process** | YAML → API Server → Deployment → ReplicaSet → Pods. |
| **Pod Scheduling Process** | New Pod → Scheduler evaluates every node → best node selected. |
| **Self-Healing Process** | Pod fails → controller notices the gap → replacement Pod created → back to desired state. |
| **Scaling Process** | Traffic rises → HPA checks metrics → replica count increases → more Pods added. |
| **Service Discovery Process** | Client calls a name → CoreDNS resolves it → Service load-balances to a healthy Pod. |

---

### How to use this
Read it top to bottom once for the full picture, or just Ctrl+F the term you heard in a meeting and didn't want to ask about.

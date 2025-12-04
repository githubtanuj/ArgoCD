# Argocd
learn Argocd in easy way


---

# ✅ **BLOCK FORMAT (Simple + Easy to Remember)**

### **1. What is Argo CD?**

* Argo CD = **Continuous Delivery (CD) tool** for Kubernetes.
* Works with **GitOps** principles.
* It **automatically deploys** Kubernetes changes from Git repo to cluster.

---

### **2. Why normal CD (Jenkins/GitLab CI/CD) has problems**

**Traditional way:**

* Developer pushes code → CI builds image → CI updates YAML → CI uses kubectl to apply changes.

**Problems:**

1. **Install tools** on Jenkins (kubectl, helm).
2. **Credentials problem** → Jenkins needs access to cluster, AWS, etc.
3. **Security problem** → credentials stored outside cluster.
4. **Scaling problem** → 50 apps × 50 clusters = too many credentials.
5. **No visibility** → Jenkins doesn’t know:

   * Did deployment fail?
   * Are pods healthy?
   * Is app running?

---

### **3. How Argo CD fixes everything**

* Instead of CI **pushing** to cluster → Argo CD **pulls** changes.
* Argo CD runs **inside** the cluster.
* Argo CD keeps cluster **in sync** with Git repo.

---

### **4. New GitOps Workflow with Argo CD**

1. **Code changes happen** → CI builds new image.
2. CI updates **Kubernetes YAML** in **separate config repo**.
3. Argo CD sees change → **pulls it** → applies in cluster.

✔ CI = build & test
✔ Argo CD = deploy safely

**Separation of concerns:**

* Developers own **CI**
* DevOps/Operations own **CD (Argo CD)**

---

### **5. Why GitOps uses separate repos**

* App code repo = **source code**
* GitOps repo = **Kubernetes YAML files** (deployment, service, secrets, ingress, configmaps)

Because:

* Config changes don’t need full CI run.
* K8s config is different from app code.

---

### **6. Benefits of using GitOps + Argo CD**

#### **A. One Git Repo = Single Source of Truth**

* No random `kubectl apply` from laptops.
* Everything goes through Git.

#### **B. Detect manual changes**

* If someone manually edits cluster → Argo CD:

  * Detects drift
  * Fixes it automatically (self-heal)
  * OR alerts team (if auto-fix disabled)

#### **C. Full audit + history**

* Every change is tracked in Git.
* Easy collaboration (Pull Requests).

#### **D. Easy Rollbacks**

* Just revert commit → Argo CD applies old working state.

#### **E. Disaster Recovery**

* Cluster crashes → create new cluster → point to Git → Argo CD rebuilds everything.

#### **F. Better access control**

* Give team access to **Git**, NOT cluster.
* Senior engineers approve merges.
* No need to give devs cluster credentials.

#### **G. CI Tools don’t need cluster access**

* Jenkins/GitLab CI never touches cluster directly.

---

### **7. Why Argo CD is powerful**

* Runs **inside Kubernetes**.
* Extends Kubernetes API using **CRDs**.
* Has **real-time UI**:

  * Shows pods
  * Shows sync status
  * Shows health
  * Shows logs, events, manifests

---

### **8. Argo CD Architecture**

* Git = **desired state**
* Cluster = **actual state**
* Argo CD = **keeps them in sync**

---

### **9. Argo CD Configuration (Application CRD)**

You create a YAML like:

* API version
* Kind: Application
* Source:

  * Git repo URL
  * Branch
  * Directory path (e.g., dev/)
* Destination:

  * Cluster URL
  * Namespace
* Sync policy:

  * auto-sync
  * self-heal
  * prune
  * createNamespace = true

Argo CD reads Git → applies to cluster.

---

### **10. Multi-Cluster Setup**

#### **Case 1: ONE Argo CD → MANY clusters**

* Argo CD running in one cluster can deploy to 1000 clusters.

#### **Case 2: Separate Environments**

* Dev → Staging → Prod
* Two options:

  * Multiple Git branches (not ideal)
  * **Kustomize overlays** (recommended)

---

### **11. Does Argo CD replace Jenkins?**

* ❌ No
* Argo CD does **CD only** for Kubernetes.
* Jenkins/GitLab CI does **CI**:

  * Tests
  * Build
  * Image push

Argo CD does:

* Automatic deployment
* Drift detection
* Sync
* Rollback

---

### **12. Alternatives**

* Flux CD (GitOps based CD tool)

---

# 🚀 **DEMO (Hands-on Summary)**

### **Setup**

* Minikube cluster empty
* Git repo with:

  * deployment.yaml (image: 1.0)
  * service.yaml
* Docker Hub image:

  * tags: 1.0, 1.1, 1.2

---

### **Step 1: Install Argo CD**

```
kubectl create namespace argocd
kubectl apply -n argocd -f install.yaml (from docs)
```

### **Step 2: Access Argo CD UI**

* Port-forward service:

```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

* Username = **admin**
* Password = from:

```
kubectl get secret argocd-initial-admin-secret ...
```

---

### **Step 3: Create application.yaml**

Includes:

* repo URL
* path: dev/
* destination namespace
* auto-sync
* prune
* self-heal

Push to Git.

---

### **Step 4: Apply application.yaml**

```
kubectl apply -f application.yaml
```

Argo CD now auto-syncs.

---

### **Step 5: Test Auto-sync**

1. Change image tag from 1.0 → 1.2
2. Commit
3. Argo CD auto-updates deployment

---

### **Step 6: Test Prune**

* Rename deployment
  → Argo CD deletes old one and creates new one.

---

### **Step 7: Test Self-Heal**

Manually change replicas to 4 using:

```
kubectl edit deploy my-app
```

Argo CD reverts back to 2.

---

# 🌳 **TREE DIAGRAM FORMAT (Very Easy to Memorize)**

```
Argo CD
│
├── What is it?
│     └── CD tool for Kubernetes using GitOps
│
├── Why needed?
│     ├── Too many tools on Jenkins
│     ├── Credential & security issues
│     ├── No deployment visibility
│     └── Not scalable
│
├── How Argo CD works
│     ├── Runs inside cluster
│     ├── Pulls changes from Git
│     └── Applies automatically
│
├── GitOps benefits
│     ├── One Git = Source of truth
│     ├── Drift detection
│     ├── Auto rollback
│     ├── Easy restore
│     ├── Full audit history
│     └── No kubectl from laptops
│
├── Argo CD features
│     ├── UI: pods, logs, health, rollout
│     ├── Auto-sync
│     ├── Prune old resources
│     └── Self-heal manual changes
│
├── Multi-cluster
│     ├── 1 Argo → 1000 clusters
│     └── Kustomize overlays for Dev/Staging/Prod
│
├── Does it replace Jenkins?
│     └── No → Jenkins = CI, Argo CD = CD
│
└── Demo steps
      ├── Install Argo CD
      ├── Port-forward
      ├── Login
      ├── Create Application CRD
      ├── Argo syncs resources
      ├── Update image tag → auto deploy
      ├── Rename resource → prune
      └── Manual change → self-heal
```

---

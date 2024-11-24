---

# Kubernetes Setup for Jenkins Access

This guide outlines the steps to configure a **namespace**, **service account**, **role**, **role binding**, and **secret** for Jenkins in a Kubernetes cluster.

---

## 🛠️ Prerequisites

Ensure the following are set up before proceeding:
- Access to a Kubernetes cluster (`kubectl` is configured).
- Appropriate permissions to create namespaces, roles, and service accounts.

---

## 📋 Steps to Set Up

### 1. **Create the Namespace**

Create a namespace named `webapps` in the Kubernetes cluster:

```bash
kubectl create namespace webapps
```

---

### 2. **Create the Service Account**

Create a service account named `jenkins` in the `webapps` namespace:

```bash
kubectl create serviceaccount jenkins --namespace webapps
```

---

### 3. **Create the Role**

Save the following configuration to a file named `app-role.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: app-role
  namespace: webapps
rules:
  - apiGroups:
      - ""
      - apps
      - autoscaling
      - batch
      - extensions
      - policy
      - rbac.authorization.k8s.io
    resources:
      - pods
      - componentstatuses
      - configmaps
      - daemonsets
      - deployments
      - events
      - endpoints
      - horizontalpodautoscalers
      - ingress
      - jobs
      - limitranges
      - namespaces
      - nodes
      - secrets
      - persistentvolumes
      - persistentvolumeclaims
      - resourcequotas
      - replicasets
      - replicationcontrollers
      - serviceaccounts
      - services
    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
```

Apply the role using:

```bash
kubectl apply -f app-role.yaml
```

---

### 4. **Create the RoleBinding**

Create a RoleBinding to bind the `app-role` role to the `jenkins` service account. Save the following configuration to a file named `app-rolebinding.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: app-rolebinding
  namespace: webapps
subjects:
  - kind: ServiceAccount
    name: jenkins
    namespace: webapps
roleRef:
  kind: Role
  name: app-role
  apiGroup: rbac.authorization.k8s.io
```

Apply the RoleBinding using:

```bash
kubectl apply -f app-rolebinding.yaml
```

---

### 5. **Create and Apply the Secret Token**

Create a secret token for the `jenkins` service account in the `webapps` namespace. Save the following configuration to a file named `jenkins-secret.yaml`:

```yaml
apiVersion: v1
kind: Secret
type: kubernetes.io/service-account-token
metadata:
  name: jenkins-secret
  namespace: webapps
  annotations:
    kubernetes.io/service-account-name: jenkins
```

Apply the secret using:

```bash
kubectl apply -f jenkins-secret.yaml
```

---

## 🧾 Verification

1. **Check the Namespace**:
   ```bash
   kubectl get namespace
   ```

2. **Verify the Service Account**:
   ```bash
   kubectl get serviceaccount jenkins --namespace webapps
   ```

3. **Confirm Role and RoleBinding**:
   ```bash
   kubectl get role app-role --namespace webapps
   kubectl get rolebinding app-rolebinding --namespace webapps
   ```

4. **Check the Secret**:
   ```bash
   kubectl get secret jenkins-secret --namespace webapps
   ```

---

## 🎉 Congratulations

You’ve successfully set up the namespace, service account, role, role binding, and secret for Jenkins in the `webapps` namespace.

--- 

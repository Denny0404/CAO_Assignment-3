
# Gitea Kubernetes Lab — Production & Public Deployment

## Public ngrok Link
**Access my public Gitea instance here:**  
https://8eec677caab6.ngrok-free.app

*(Replace with your latest ngrok link if needed)*

---

## Overview

This lab demonstrates running Gitea in Kubernetes with persistent storage and an external MySQL database (production mode), and making it accessible to the public via ngrok.

---

## Features Implemented

- **Development mode**: Gitea using SQLite (default, for quick dev/testing).
- **Production mode**: Gitea with MySQL and persistent storage (Helm-based, ready for real use).
- **Public access**: Instance accessible globally via ngrok tunnel.

---

## How to Run This Project

### 1. Prerequisites

- Codespaces (or any Linux with kubectl, helm, ansible, ngrok)
- [Ngrok account and authtoken](https://dashboard.ngrok.com/get-started/your-authtoken)

---

### 2. Clone and Setup

```bash
git clone <your-repo-url>
cd <project-folder>
pip install ansible kubernetes
git submodule update --init --recursive
```

---

### 3. Install MySQL via Helm (Production Only)

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm install gitea-mysql bitnami/mysql   --set auth.rootPassword=supersecretpassword   --set auth.database=giteadb   --set auth.username=giteauser   --set auth.password=giteapass
```

Wait until MySQL is running:

```bash
kubectl get pods
```

---

### 4. Configure `values.yaml` (Production Settings)

Make sure you set:

```yaml
persistence:
  enabled: true
  size: 10Gi

gitea:
  config:
    database:
      DB_TYPE: mysql
      HOST: gitea-mysql.default.svc.cluster.local:3306
      NAME: giteadb
      USER: giteauser
      PASSWD: giteapass
```

---

### 5. Deploy Gitea with Ansible

```bash
ansible-playbook down.yaml
ansible-playbook up.yaml
```

Wait for Gitea pod to be running:

```bash
kubectl get pods
```

---

### 6. Port Forward to Access Gitea

```bash
kubectl port-forward svc/gitea-http 3000:3000
```

* Open browser: [http://localhost:3000](http://localhost:3000) (or use Codespaces port tab)
* Sign in, create a test repo

---

### 7. Expose Publicly via ngrok

**(Do NOT run ngrok from up.yaml! Always run separately as a dev tool.)**

```bash
ngrok config add-authtoken <your_authtoken>
ngrok http 3000
```

* Copy the public URL (e.g., `https://xxxx.ngrok-free.app`)
* Open it in any browser—Gitea is public!

---

## How to Prove All Requirements (Screenshots)

* Gitea UI with your test repository
* `kubectl get pods` showing both Gitea and MySQL pods running
* ngrok terminal with public forwarding URL
* Gitea login page accessed via the ngrok URL
* Your `values.yaml` config (persistence + MySQL)
* **Include all screenshots in the `/screenshots` folder!**

---

## Teardown

```bash
ansible-playbook down.yaml
```

---



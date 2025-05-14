# Automating F5 CIS and NGINX Ingress with Ansible (Air-Gapped Ready)

This repository is designed for customers operating in **closed, air-gapped, or offline network environments**. It provides modular Ansible-based automation to deploy and configure a full Kubernetes ingress stack, including **F5 CIS**, **NGINX Ingress Controller**, **Cert-Manager**, and a **demo application**, with full support for secure, internal-only artifact delivery.

## 📁 Folder Overview

This repository includes five primary folders, each with a specific automation objective:

* **`ATC/`**: Automates the configuration of **F5 BIG-IP** using Application Services 3 (AS3) and Declarative Onboarding (DO). These declarations help create virtual servers, pools, monitors, and self-IPs.
* **`ccn-app/`**: Deploys a **demo application** within the closed Kubernetes network. This role creates a sample workload that can be used to validate ingress, TLS, and application routing.
* **`ccn-cis/`**: Deploys the ingress stack, including:

  * **F5 Container Ingress Services (CIS)** to program BIG-IP via Kubernetes resources
  * **NGINX Ingress Controller** (open-source or commercial NGINX Plus)
  * **Cert-Manager** to provision and rotate TLS certificates
  * **Syslog server** for observability/logging validation
* **`ccn-push/`**: Runs from a connected network and pushes all needed:

  * **Container images** to a private container registry (Harbor, Nexus, etc.)
  * **Helm charts** to an internal chart repository (ChartMuseum, Harbor, etc.)
  * Ensures the target closed network has access to all required artifacts
* **`open-pull/`**: Runs from a connected network and does the following:

  * Pulls container images from DockerHub/F5/Nginx registries
  * Downloads Helm charts from public repositories
  * Packages them into `.tar` archives for **USB/CD/DVD/Offline transfer** to the closed network

---

## ✅ Requirements

To use this automation, the following must be present:

* A functioning **Kubernetes cluster**

  * Default CNI is **Flannel**, but other CNIs (Calico, Cilium, etc.) are supported
* A **DNS subzone** delegated to the **BIG-IP DNS** (GSLB or Wide IP) to handle name resolution for ingress services
* **Ansible** and **Ansible Navigator** installed on the control node/client

  * Tested with Ansible 2.15+
  * Navigator for improved execution and content management

---

## 📦 Components Automated

This repository provides end-to-end automation for the following:

* Deployment of **F5 BIG-IP Container Ingress Services (CIS)**
* Deployment of **NGINX and NGINX Plus Ingress Controller**
* Configuration of **Kubernetes ingress resources**, TLS certificates, services, and BIG-IP DNS entries
* Packaging and delivery of container artifacts, binaries, and Helm charts for **offline/air-gapped installs**

---

## 🎯 Objectives

* Support **air-gapped**, **offline**, or **closed-network** deployments
* Deliver **modular, reusable Ansible roles and playbooks** aligned with industry best practices
* Enable **declarative, GitOps-style workflows** for continuous deployment
* Facilitate integration with **CI/CD tools** like Jenkins, GitHub Actions, or GitLab CI even in restricted environments
* Promote repeatability, automation, and portability across customer sites

---

## 📂 Repository Structure

```
f5_ansible_cis/
├── ATC
│   ├── AS3                     # Application Services 3 declarations (L7 config)
│   └── DO                      # Declarative Onboarding declarations (L4 config)
├── ccn-app
│   ├── roles
│   │   └── manage_k8s_app     # Deploys demo app via K8s manifests
│   │       ├── files
│   │       ├── tasks
│   │       └── templates
│   └── vars
│       └── apps               # Custom app parameters (replicas, labels, etc.)
├── ccn-cis
│   ├── roles
│   │   ├── deploy_charts      # Automates Helm chart deployment
│   │   │   └── tasks
│   │   ├── manage_cert_manager
│   │   │   ├── tasks
│   │   │   └── templates      # Certificates, Issuers, ClusterIssuers
│   │   └── manage_k8s_app     # Common K8s app management logic
│   │       ├── files
│   │       ├── tasks
│   │       └── templates
│   └── vars
│       ├── apps               # App configuration (name, port, protocol)
│       ├── certs              # TLS-related settings
│       ├── charts             # Helm chart locations
│       ├── logins             # Registry and controller credentials
│       └── values             # Helm values files for templating
├── ccn-push
│   ├── roles
│   │   ├── docker_login       # Log in to target registry
│   │   │   └── tasks
│   │   ├── docker_push        # Push container images
│   │   │   └── tasks
│   │   ├── manage_k8s_app     # Optional K8s manifest packaging
│   │   │   ├── files
│   │   │   ├── tasks
│   │   │   └── templates
│   │   └── push_helm_charts   # Upload Helm charts to private repo
│   │       └── tasks
│   └── vars
│       ├── apps
│       ├── certs
│       ├── charts
│       ├── images             # Image names, tags, source/destination info
│       └── logins
└── open-pull
    ├── roles
    │   ├── docker_login
    │   │   └── tasks
    │   ├── docker_pull        # Pull public container images
    │   │   └── tasks
    │   └── pull_helm_charts   # Fetch public Helm charts
    │       ├── tasks
    │       └── templates
    └── vars
        ├── charts
        ├── images
        └── logins
```

---

## 🚀 Getting Started

### 1. Prepare Your Environment

* **Clone the repository** into a machine on the connected or closed network
* If air-gapped, use `open-pull/` to gather images/charts and copy TAR files over
* Use `ccn-push/` to load those images and charts into the private registries on the closed network
* Customize values in `vars/` folders to match your target infrastructure and domains

### 2. Example Execution

```bash
# Pull artifacts on a connected host
ansible-playbook open-pull/pull_artifacts.yaml

# Push artifacts to internal registry/repo
ansible-playbook ccn-push/push_artifacts.yaml

# Deploy NGINX, CIS, Cert Manager, Syslog
ansible-playbook ccn-cis/deploy_stack.yaml

# Deploy demo app
ansible-playbook ccn-app/deploy_demo_app.yaml

# Apply BIG-IP configuration
ansible-playbook ATC/configure_bigip.yaml
```

---

## 📖 Documentation

* [F5 CIS Documentation](https://clouddocs.f5.com/containers/latest/)
* [NGINX Ingress Controller](https://docs.nginx.com/nginx-ingress-controller/)
* [Ansible Navigator](https://ansible.readthedocs.io/projects/navigator/)
* [Cert-Manager](https://cert-manager.io/docs/)

---

## 🤝 Contributing

Pull requests are welcome! If you find bugs, improvements, or new use cases (e.g., supporting different CNIs or DNS providers), please fork and submit a PR.

---

## 🛡️ License

This project is licensed under the MIT License. See `LICENSE` for more details.

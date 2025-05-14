# F5 Ansible CIS for Closed Networks

This repository demonstrates how to automate the deployment of F5 BIG-IP Container Ingress Services (CIS), NGINX Ingress Controller, and supporting Kubernetes applications using Ansible, designed specifically for **closed-network (air-gapped, offline)** environments.

---

## 🧭 Repository Structure

```text
f5_ansible_cis/
├── ATC                    # Automation Toolchain templates (AS3, DO)
├── ccn-app                # Deploys a demo app in the closed network
├── ccn-cis                # Deploys CIS, NGINX, Cert-Manager, Syslog server
├── ccn-push               # Pushes images and charts to internal registry (air-gapped support)
├── open-pull              # Pulls public artifacts and packages them as TARs
├── LICENSE
└── README.md
```

---

## 📦 Folder Overview

### `ATC`

* Contains AS3 and DO templates to configure the BIG-IP system.

### `ccn-app`

* Deploys a sample app into the Kubernetes cluster using Ansible.
* Includes pre-defined templates for Deployment, Service, Ingress, IngressLink, and DNS.

### `ccn-cis`

* Automates the installation of:

  * F5 BIG-IP Container Ingress Services (CIS)
  * NGINX Ingress Controller
  * Cert-Manager
  * Syslog Server

### `ccn-push`

* Pushes required Docker images and Helm charts to an internal registry in the closed network.
* Ensures all components can be deployed offline.

### `open-pull`

* Pulls open-source container images and Helm charts.
* Packages artifacts into `.tar` files to be transferred into closed networks.

---

## 📘 Use Case: ExternalDNS with F5 CIS and NGINX

The purpose of this document is to demonstrate **ExternalDNS with NGINX Ingress Controller using F5 CIS with BIG-IP**.

ExternalDNS allows users to control DNS records dynamically via Kubernetes CRD resources in a DNS provider-agnostic way. This user-guide documents ExternalDNS integration with F5 CIS + BIG-IP LTM and DNS, load balancing traffic to NGINX Ingress Controller.

BIG-IP LTM and DNS are configured on the same device for a single cluster in this demo, but can also be on separate devices to support multi-site, multi-cluster, or multi-datacenter scenarios.

This ExternalDNS architecture enables modern container applications to leverage both:

* **F5 BIG-IP CIS** for enterprise-level traffic management
* **NGINX Ingress Controller** for lightweight ingress and edge routing

It creates a unified and elegant **control plane** solution that enhances collaboration between **NetOps** and **DevOps** teams.

📌 **Diagram**: ![Architecture Diagram](https://github.com/mdditt2000/k8s-bigip-ctlr/blob/main/user_guides/externaldns-nginx/diagram/2022-01-13_10-37-44.png)

---

## 🔧 Requirements

* Kubernetes Cluster (default CNI: Flannel; others are supported)
* DNS Subzone pointed to the BIG-IP DNS
* Ansible + Ansible Navigator installed on the client
* Internal Docker/Helm Registry (for closed networks)

---

## 🛠️ Components Automated

* F5 BIG-IP Container Ingress Services (CIS)
* NGINX & NGINX Plus Ingress Controllers
* Kubernetes Resources (Ingress, TLS, Services, DNS)
* Cert-Manager with custom TLS secrets
* Syslog Server for logging
* Offline delivery of all YAMLs, Helm charts, and container images

---

## 🎯 Objectives

* ✅ Support for air-gapped / offline / closed-network environments
* ✅ Deliver modular, reusable Ansible roles and playbooks
* ✅ Enable declarative, GitOps-style workflows
* ✅ Integrate with CI/CD for secure and automated deployments

---

## 🗂️ About `vars/` Files

Each folder contains a `vars/` directory with input YAMLs that define:

* `apps/`: Application deployment metadata (used in `ccn-app`, `ccn-cis`, and `ccn-push`)
* `charts/`: Helm chart versions and names for NGINX, CIS, and Cert-Manager
* `certs/`: DNS zone and TLS cert configuration
* `logins/`: Registry and BIG-IP login credentials (use sample files for template)
* `values/`: Helm chart configuration overrides
* `images/`: Image source locations and tags (for Docker and NGINX repos)

> ✏️ Update the `vars/` files before running the playbooks to match your environment’s:
>
> * Internal domain (e.g., `mgt.test.local`)
> * BIG-IP hostnames, IPs, and credentials
> * Registry URLs and login credentials
> * App-specific details (e.g., hostname, TLS requirements)

---

## 📌 Getting Started

1. **Pull public artifacts and package them**

```bash
cd open-pull
ansible-navigator run docker_pull.yaml
ansible-navigator run helm_pull.yaml
```

2. **Transfer `.tar` files to the closed network and push**

```bash
cd ccn-push
ansible-navigator run docker_push.yaml
ansible-navigator run helm_push.yaml
```

3. **Deploy CIS, NGINX, and supporting tools**

```bash
cd ccn-cis
ansible-navigator run cis_deploy.yaml
ansible-navigator run cert-manager_deploy.yaml
```

4. **Deploy your app**

```bash
cd ccn-app
ansible-navigator run app_deploy.yaml
```

---

## 📮 Feedback

Please feel free to open an issue or pull request if you encounter problems or have ideas for improvement.

---

## 📜 License

This repository is licensed under the MIT License.

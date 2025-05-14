# Automating F5 CIS and NGINX Ingress with Ansible (Air-Gapped Ready)

This repository provides a modular, Ansible-based automation framework for deploying and configuring both **F5 Container Ingress Services (CIS)** and **NGINX Ingress Controller** in **air-gapped or disconnected Kubernetes environments**.

## 📦 Components Automated

* **F5 BIG-IP Container Ingress Services (CIS)**
* **NGINX and NGINX Plus Ingress Controller**
* **Kubernetes Ingress Resources, TLS, Services, and DNS Records**
* **Air-gapped artifact delivery (container images, binaries, YAMLs)**

## 🎯 Objectives

* Support **air-gapped**, **offline**, or **closed-network** deployments
* Deliver modular, reusable **Ansible roles** and **playbooks**
* Enable declarative and repeatable **GitOps-style workflows**
* Integrate with CI/CD tools for automation in restricted environments

---

## 📁 Repository Structure

```
ansible-cis-nginx-automation/
├── roles/
│   ├── cis-install/
│   ├── nginx-install/
│   ├── kube-configure/
│   └── dns-setup/
├── inventory/
│   └── hosts.yaml
├── playbooks/
│   ├── deploy-cis.yaml
│   ├── deploy-nginx.yaml
│   └── full-stack.yaml
├── files/
│   ├── images.tar.gz           # Pre-pulled images for offline load
│   └── manifests/              # YAMLs and CRDs
├── vars/
│   └── config.yaml
└── README.md
```

---

## 🚀 Getting Started

### 1. Prepare Your Environment

* Clone this repo into a machine within the target air-gapped network
* Load container images into your local registry (use `images.tar.gz`)
* Customize `vars/config.yaml` and `inventory/hosts.yaml` with your environment details

### 2. Run the Full Automation Stack

```bash
ansible-playbook -i inventory/hosts.yaml playbooks/full-stack.yaml
```

You can also run individual playbooks:

```bash
ansible-playbook -i inventory/hosts.yaml playbooks/deploy-cis.yaml
ansible-playbook -i inventory/hosts.yaml playbooks/deploy-nginx.yaml
```

---

## 🔐 Air-Gapped Considerations

* All dependencies (container images, binaries, manifests) are stored locally
* DNS setup can be automated via static records or internal CoreDNS updates
* Private container registry access is assumed (or image loading via tarball)

---

## 🧩 Extensibility

You can extend this automation by adding:

* Additional ingress resources or annotations
* Role-based access control (RBAC) customizations
* Monitoring/logging integrations (Prometheus, Grafana, etc.)

---

## 📖 Documentation

* [F5 CIS Docs](https://clouddocs.f5.com/containers/latest/)
* [NGINX Ingress Docs](https://docs.nginx.com/nginx-ingress-controller/)
* [Ansible Best Practices](https://docs.ansible.com/)

---

## 🤝 Contributing

Pull requests are welcome! Please fork the repo and open a PR with your suggested improvements, especially around better air-gapped workflows or additional ingress examples.

---

## 🛡️ License

This project is licensed under the MIT License. See `LICENSE` for more details.

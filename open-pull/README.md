## 🗂️ open-pull

The playbooks in the `open-pull/` directory download the container images and Helm charts required to deploy and demonstrate F5 Container Ingress Services (CIS) and related components in a closed or air-gapped network.

### 🔧 How It Works

* **Helm Charts**
  Located in `vars/charts/`, the YAML files list the Helm charts to be downloaded.

  * You can **modify the version** and the **destination path** (where charts are saved).

* **Container Images**
  Listed in `vars/images/`, these files define the **name and version** of all required container images.

  * You can **change image versions** to match your desired build.

* **Login Credentials**
  Credential files are located in `vars/logins/`.
  To use them, **remove the word `sample` from the filename** and update the contents as shown below:

<details>
<summary><strong>Example Login Files</strong></summary>

```yaml
# bigip_login.yaml
bigip_username: admin
bigip_password: password
```

```yaml
# nginx_login.yaml
docker_username: nginx_jwt        # Your NGINX JWT from subscription or trial
docker_password: nginx_jwt        # Your NGINX JWT from subscription or trial
registry_url: private-registry.nginx.com
```

```yaml
# docker_login.yaml
docker_username:                  # Optional – only needed if pulling from DockerHub
docker_password:                  # Optional – only needed if pulling from DockerHub
```

> ℹ️ If you're not using DockerHub, you can skip editing `docker_login.yaml` and comment out the Docker login task in `docker_pull.yaml`.

</details>

---

### 📂 Inventory

Update the `inventory` file to reflect the **master node's IP address** of your Kubernetes cluster.

---

### ▶️ Running the Playbooks

Run the following playbooks in order to pull required artifacts:

```bash
ansible-playbook docker_pull.yaml
ansible-playbook helm_pull.yaml
ansible-playbook nginx_pull.yaml
```

After execution, you will find all the downloaded **images and charts packaged as TAR files** in the `download/` directory.

---

This makes it easy to transfer and reuse the artifacts in air-gapped environments, ensuring a repeatable and declarative deployment process.

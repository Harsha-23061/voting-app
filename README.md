**🚀 Azure DevOps Project with Kubernetes & ArgoCD Integration**

---

### 🧾 **1. Azure Account Initialization**
- Set up a valid **Azure Account** with an active subscription.
- Log into [Azure DevOps](https://dev.azure.com/) using the **same credentials** as your Azure portal.

---

### 📁 **2. Create a New Azure DevOps Project**
- Navigate to Azure DevOps Dashboard → Click on **New Project**.
- Name the project appropriately based on your objectives.

---

### 🔁 **3. GitHub Repo Migration to Azure Repos**
- Go to **Repos** → **Import Repository**.
- Paste your **GitHub HTTPS URL** to import all code into Azure Repos.

---

### 🔧 **4. Build & Push Pipeline Configuration**
1. Navigate to **Pipelines** → **New Pipeline**.
2. Select **Azure Repos Git** as the source.
3. Choose your required repository.
4. Under **Configure Pipeline**, select:
   - **Docker - Build and push an image to Azure Container Registry**.
5. Choose the **correct Azure subscription**.

---

### 📦 **5. Create Azure Container Registry (ACR)**
- Go to [Azure Portal](https://portal.azure.com/) → Search **Container Registry** → Create a new instance.
- Group all project services in a **standard Resource Group** for manageability.

---

### 📝 **6. YAML Pipeline Customization**
- Modify the generated YAML to:
  - Replace default VM image with a **custom agent pool**.
  - Split the **build** and **push** stages.
  - Create separate pipelines for **worker**, **results**, and **vote** services.

---

### 🖥️ **7. Setup Virtual Machine for Self-hosted Agent**
- Create a VM and install:
  - Docker
  - Kubernetes CLI (`kubectl`)
- Go to **Project Settings > Agent Pools** → Create a **New Pool** (mark as **Self-hosted**).

---

### 🔐 **8. Generate Personal Access Token (PAT)**
- Go to **User Settings > Personal Access Tokens** → Generate a new token with relevant scopes.

---

### 🔗 **9. Connect VM to Azure DevOps as Agent**
1. From **Agent Pools**, click on **New Agent**.
2. Follow the commands provided:
   ```bash
   ./config.sh  # Enter server URL and PAT
   ./run.sh     # Run agent in active state
   ```

---

### ☸️ **10. Create Kubernetes Cluster (AKS)**
- In Azure Portal → Search **Kubernetes services** → Create a new AKS cluster.
- On the VM:
  ```bash
  curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
  az login
  az aks get-credentials --name azuredevops --resource-group azurecicd --overwrite-existing
  sudo snap install kubectl --classic
  kubectl get pods
  ```

---

### 🚦 **11. Install & Configure ArgoCD**
1. Check ArgoCD pods:
   ```bash
   kubectl get pods -n argocd
   ```
2. Retrieve & decode initial admin password:
   ```bash
   kubectl get secrets -n argocd
   kubectl edit secret argocd-initial-admin-secret -n argocd
   echo <base64-password> | base64 --decode
   ```
3. Edit NodePort:
   ```bash
   kubectl edit svc argocd-server -n argocd
   kubectl get nodes -o wide
   ```
4. Adjust config (like session timeout):
   ```bash
   kubectl edit cm argocd-cm -n argocd
   ```

---

### 🔒 **12. Create Docker Secret for K8s**
```bash
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<registry>.azurecr.io \
    --docker-username=<SP-ID> \
    --docker-password=<SP-Password>
```
- Verify using:
  ```bash
  kubectl get pods -w
  ```

---

### 🌐 **13. Access ArgoCD UI**
- Allow the ArgoCD NodePort in **Inbound NSG Rules**.
- Access the ArgoCD UI at: `http://<external-ip>:<node-port>`

---

### 🧩 **14. Configure ArgoCD Application**
- In ArgoCD UI → Go to **Settings > Repositories** → Add Git Repo.
- Create **Application**:
  - Git Repo URL
  - Revision/Path
  - Cluster + Namespace

---

### 🗂️ **15. Add Automation Scripts**
- In your repo → Create folder `scripts/`
- Add necessary `.sh` files
- In YAML, add a new stage `update` to run scripts.

---

### 🔁 **16. Redeploy and Validate**
- Redeploy the pods
- Access the ArgoCD UI and validate your deployment visually

---

### 🖼️ **Visual Breakdown**
- 💡 Include diagrams showing worker, vote, and results jobs.
- 🖥️ Screenshots of ArgoCD UI, pipeline execution, and VM agent.

---

### ✅ **Final Thoughts**
This setup enables a complete **CI/CD workflow** using **Azure DevOps**, **Kubernetes**, and **ArgoCD**. 
You now have a scalable, secure, and automated deployment pipeline with modern DevOps best practices.

---


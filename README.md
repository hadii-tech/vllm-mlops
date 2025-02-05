# DeepSeek-R1-Distill-Qwen-14B on Kubernetes (Production-Ready)

This repo deploys **DeepSeek-R1** models from HuggingFace on a **DigitalOcean Kubernetes cluster** with an **NVIDIA H100 (80GB VRAM)**, fully optimized for **production AI inference**.

## 🚀 **Features**
✅ **Prometheus Metrics Collection (via ServiceMonitor)**  
✅ **Autoscaling (HPA based on CPU & Memory)**  
✅ **Shared Memory Optimization**  
✅ **ArgoCD GitOps Deployment**  
✅ **Ingress for External Access (Optional)**  

---

## 📌 **Prerequisites**
- **DigitalOcean Kubernetes cluster** with **NVIDIA H100 (80GB VRAM)** GPU node (labelled as `gpu: "true"`) 
- **Nginx-ingress** configured for ingress (Optional).
- **ArgoCD configured** to sync manifests.
- **Prometheus Operator installed** in the cluster.
- **NVIDIA k8s-device-plugin** installed.
Consider using our [Production Ready Kubernetes Cluster Blueprint](https://github.com/hadii-tech/cloud-infra) to deploy this easily.
---

## 🚀 **Deployment Steps**
1. **Fork or clone** this repository.
2. In **ArgoCD**, create an application pointing to:
   - **Repo URL:** `https://github.com/YOUR_USER_OR_ORG/vllm-mlops.git`
   - **Path:** `k8s`
   - **Namespace:** `argocd`
3. **Sync the ArgoCD application**:
   ```bash
   argocd app sync vllm-models
   ```
4. **Verify**:
```
   kubectl get pods -n vllm
```
---


If you applied the Ingress, you can curl your domain (e.g. deepseek.example.com) at https://deepseek.example.com/v1/completions.
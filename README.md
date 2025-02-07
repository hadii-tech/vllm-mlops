# Production Ready DeepSeek-R1 Models on Kubernetes
This repo deploys [OpenAPI compatible](https://docs.vllm.ai/en/latest/serving/openai_compatible_server.html) API server based on the **DeepSeek-R1** model for production capable inferencing workloads on a **DigitalOcean Kubernetes cluster** deployed with NVIDIA H100 GPUs.

**DeepSeek-R1** is a cutting-edge LLM trained using large-scale reinforcement learning to enhance reasoning capabilities. It consists of two main versions:

1. **DeepSeek-R1-Zero**: A model trained purely through RL without any initial SFT, allowing it to autonomously develop reasoning behaviors.
1. **DeepSeek-R1**: A refined version that incorporates cold-start training data before reinforcement learning, improving readability and performance.

## 🚀 **Features**
✅ **[vLLM](https://docs.vllm.ai/en/latest/) based inference and serving**:
   - **Continuous Batching** of incoming requests, ensuring efficient model execution across multiple users.
   - **CUDA/HIP Graph Execution**, leveraging optimized GPU execution paths for speed and efficiency.
   - **Paged Attention Mechanism**, allowing scalable inference without excessive memory usage.
   - **Token Streaming Support**, making it ideal for real-time AI applications.
   - **Tensor parallelism** and **pipeline parallelism** to support for distributed inference
  
✅ **Prometheus Metrics Collection (via ServiceMonitor)**
   - Scraping real-time **inference latency**, **GPU utilization**, **request rates**, and **memory usage**.
   - Providing **Grafana-ready dashboards** for monitoring AI workload health.
   - Enabling **alerts and anomaly detection** using Prometheus Query Language (PromQL).
     
✅ **Autoscaling (HPA based on CPU & Memory)**  
   - **CPU Utilization**: Ensuring optimal performance without over-provisioning resources.
   - **Memory Utilization**: Automatically scaling based on inference workload size.
   - **Custom Scaling Policies**: Allowing fine-tuned configurations to match production needs.
     
✅ **ArgoCD GitOps Deployment**  
   - **Declarative Infrastructure Management**, ensuring consistency across environments.
   - **Automated Syncing**, deploying the latest configurations whenever changes are pushed to Git.
   - **Rollback & Self-Healing**, allowing automatic recovery from failed deployments.
     
✅ **Ingress for External Access (Optional)**
   - **NGINX-based Load Balancing**, enabling high availability and efficient routing of inference requests.
   - **TLS/SSL Support with Cert-Manager**, securing API communications.
   - **Path-based Routing**, allowing seamless integration with other applications and microservices.

---

## 📌 **Prerequisites**
- A Kubernetes cluster with
   -  **GPU nodes** with node lables `gpu: "true"` and sufficient VRAM to deploy the specific LLM you intend to deploy. For this experiment, we used the **NVIDIA H100 (80GB VRAM)** GPU nodes from Digital Ocean to deploy the [Distilled Qwen 14B](https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Qwen-14B) version of DeepSeek-R1.
   - **ArgoCD** configured to sync manifests from this repo which deploy the inference infrastructure.
     - The [container args](https://github.com/hadii-tech/vllm-mlops/blob/main/k8s/deployment.yaml#L35) for the vLLM container in `k8s/deployment.yaml` can be further configured for model quantization, gpu memory utilization, and more - refer to this [documentation](https://docs.vllm.ai/en/latest/serving/openai_compatible_server.html?ref=blog.mozilla.ai).
   - **Prometheus Operator installed** in the cluster.
   - **Nginx-ingress** configured for ingress and **cert-manager** for SSL certificate management (Optional)

Consider using our [Production Ready Kubernetes Cluster Blueprint](https://github.com/hadii-tech/cloud-infra) to deploy the required Kubernetes infrastructure easily on Digital Ocean.

---

## 🚀 **Deployment Steps**
1. **Fork or clone** this repository.
2. Configure `/k8s/ingress.yaml` appropriately if desired, or remove it entirely. 
3. In **ArgoCD**, create an application pointing to:
   - **Repo URL:** `https://github.com/YOUR_USER_OR_ORG/vllm-mlops.git`
   - **Path:** `k8s`
   - **Namespace:** `argocd`
4. **Sync the ArgoCD application**:
   ```bash
   argocd app sync vllm-models
   ```
5. **Verify**:
```
   kubectl get pods -n vllm
```
If you applied the Ingress, you can curl your domain (e.g. deepseek.example.com) at https://deepseek.example.com/v1/completions to test the OpenAPI server.

# **EKS-InfraManager: Three-Tier App Deployment on AWS EKS**  

This project automates the **deployment of a three-tier application** (Frontend, Backend, and Database) on **AWS EKS** using **Kubernetes, Helm, and AWS Load Balancer Controller**. It ensures scalability, high availability, and best DevOps practices for cloud-native applications.

---

## **📌 Prerequisites**  

Before deploying, ensure you have the following installed:  

✅ **kubectl** - [Install Guide](https://kubernetes.io/docs/tasks/tools/)  
✅ **Helm** - [Install Guide](https://helm.sh/docs/intro/install/)  
✅ **AWS CLI (v2 only)** - [Install Guide](https://aws.amazon.com/cli/)  

Update Helm repositories:  
```bash
helm repo update
```

---

## **⚙️ Kubernetes Cluster Setup**  

1️⃣ **Update the Kubernetes Context:**  
```bash
aws eks update-kubeconfig --name my-eks-cluster --region us-west-2
```

2️⃣ **Verify Cluster Access:**  
```bash
kubectl auth can-i "*" "*"
kubectl get nodes
```

3️⃣ **Check Autoscaler Status:**  
```bash
kubectl get pods -n kube-system
```

4️⃣ **View Cluster Autoscaler Logs:**  
```bash
kubectl logs -f -n kube-system -l app=cluster-autoscaler
```

5️⃣ **View Load Balancer Logs:**  
```bash
kubectl logs -f -n kube-system -l app.kubernetes.io/name=aws-load-balancer-controller
```

---

## **🚀 Building & Pushing Docker Images**  

### **For macOS Users**  
Enable Docker CLI experimental features:  
```bash
export DOCKER_CLI_EXPERIMENTAL=enabled
aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/w8u5e4v2
```

#### **Frontend Docker Image**
```bash
docker buildx build --platform linux/amd64 -t workshop-frontend:v1 .
docker tag workshop-frontend:v1 public.ecr.aws/w8u5e4v2/workshop-frontend:v1
docker push public.ecr.aws/w8u5e4v2/workshop-frontend:v1
```

#### **Backend Docker Image**
```bash
docker buildx build --platform linux/amd64 -t workshop-backend:v1 .
docker tag workshop-backend:v1 public.ecr.aws/w8u5e4v2/workshop-backend:v1
docker push public.ecr.aws/w8u5e4v2/workshop-backend:v1
```

---

### **For Linux & Windows Users**  

#### **Frontend Docker Image**
```bash
docker build -t workshop-frontend:v1 .
docker tag workshop-frontend:v1 public.ecr.aws/w8u5e4v2/workshop-frontend:v1
docker push public.ecr.aws/w8u5e4v2/workshop-frontend:v1
```

#### **Backend Docker Image**
```bash
docker build -t workshop-backend:v1 .
docker tag workshop-backend:v1 public.ecr.aws/w8u5e4v2/workshop-backend:v1
docker push public.ecr.aws/w8u5e4v2/workshop-backend:v1
```

---

## **🔹 Deploying on Kubernetes (AWS EKS)**  

### **1️⃣ Create a New Namespace**  
```bash
kubectl create ns workshop
kubectl config set-context --current --namespace workshop
```

### **2️⃣ Deploy MongoDB Database**  
```bash
cd k8s_manifests/mongo_v1
kubectl apply -f secrets.yaml
kubectl apply -f deploy.yaml
kubectl apply -f service.yaml
```

### **3️⃣ Deploy Backend API (Node.js)**  
```bash
kubectl apply -f backend-deployment.yaml
kubectl apply -f backend-service.yaml
```

### **4️⃣ Deploy Frontend Application**  
```bash
kubectl apply -f frontend-deployment.yaml
kubectl apply -f frontend-service.yaml
```

### **5️⃣ Deploy Load Balancer to Expose Application**  
```bash
kubectl apply -f full_stack_lb.yaml
```

---

## **⚠️ Debugging & Troubleshooting**  

🔹 **Check logs if pods are not running:**  
```bash
kubectl logs -f POD_ID
```

🔹 **Check Kubernetes service & deployment status:**  
```bash
kubectl get pods,svc,deploy -n workshop
```

🔹 **Describe a failing pod for detailed logs:**  
```bash
kubectl describe pod POD_ID -n workshop
```

---

## **📊 Monitoring with Grafana**  

🔹 **Default Login Credentials:**  
- **Username:** `admin`  
- **Password:** `prom-operator`  

🔹 **Import Dashboard for Cluster Metrics:**  
Dashboard ID: `1860`  

🔹 **Explore more Grafana dashboards:**  
[Grafana Dashboard Library](https://grafana.com/grafana/dashboards/)  

---

## **📌 Summary**  

✅ **Automates deployment of a three-tier web app on AWS EKS**  
✅ **Uses Docker for containerization & Helm for package management**  
✅ **Integrates AWS Load Balancer Controller for efficient traffic handling**  
✅ **Monitors cluster performance using Prometheus & Grafana**  

🚀 **This setup ensures a scalable, high-availability cloud-native deployment!**  

---

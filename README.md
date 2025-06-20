# 🚀 K8s GitOps with ArgoCD –  Python Flask app


### ✅ Step-by-Step Overview (what you'll do):

1. **Create a simple Flask app**
2. **Dockerize the app**
3. **Write Kubernetes manifests** (Deployment + Service)
4. **Push everything to GitHub**
5. **Create ArgoCD app pointing to the repo**
6. **Sync and port-forward to access the app**

### 📁 Folder Structure (GitHub repo)

```
K8s-GitOps-Argocd-Flask-App/
├── app/
│   └── app.py
│   └── requirements.txt
│   └── Dockerfile
├── deployment.yaml
├── service.yaml
├── README.md
```

---

### 🔥 1. Sample Flask App (`app/app.py`)

```pythonfrom flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello from Flask in Kubernetes via ArgoCD!"


if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

### 📦 2. Dockerfile (`app/Dockerfile`)

```Dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY . .

COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

EXPOSE 5000

CMD ["python", "app.py"]

```


### 📃 3. requirements.txt

```
flask
```

---

### 🚀 4. Kubernetes Deployment (`deployment.yaml`)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: flask
  template:
    metadata:
      labels:
        app: flask
    spec:
      containers:
      - name: flask
        image: krutika09/flask-k8s:latest   # Use your DockerHub image
        ports:
        - containerPort: 5000
```


### 🌐 5. Kubernetes Service (`service.yaml`)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: flask-service
spec:
  selector:
    app: flask
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5000
```

---

### ✅ Final Steps:

1. Push all files to your GitHub repo.

2. Create the Docker image and push to DockerHub

## Port Forward ArgoCD Server
```sh
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

🌐 Access ArgoCD Application:
```
http://localhost:8080
```

## Retrieve ArgoCD Admin Password
```sh
kubectl get secret argocd-initial-admin-secret -n argocd -o yaml | grep password
echo <password> | base64 --decode
```

3. Create ArgoCD app:

```bash
argocd app create flask-app \
--repo https://github.com/Krutika09/K8s-GitOps-Argocd-Flask-App.git \
--path . \
--dest-server https://kubernetes.default.svc \
--dest-namespace default
```
## Verify ArgoCD Installation
```sh
kubectl get all -n argocd
```

4. Sync app:

```bash
argocd app sync flask-app
```

![image](https://github.com/user-attachments/assets/809a8591-3f47-4cff-9776-19e78ed0071e)


5. Port forward:

```bash
kubectl port-forward svc/flask-service 8082:80 -n default
```
```
kubectl get pods
```

6. Open your browser at:

```
http://localhost:8082
```
![image](https://github.com/user-attachments/assets/f9ed5193-8037-4477-9479-fd67fc584a12)

---

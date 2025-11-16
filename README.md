```markdown
# 🚀 Complete CI/CD Pipeline (GitHub Actions + Docker + Docker Hub + Minikube)

## Detailed Step-by-Step Guide 
---

1. PROJECT SETUP

 1.1 Create project folder
```
mkdir ci-cd-demo
cd ci-cd-demo
```

## 1.2 Initialize the application (Node.js example)
```
npm init -y
```

## 1.3 Create an app file
```
touch app.js
```

### app.js
```js
const express = require("express");
const app = express();

app.get("/", (req, res) => {
  res.send("CI/CD Pipeline Working 🚀");
});

app.listen(3000, () => console.log("Server running on port 3000"));
```

## 1.4 Install dependencies
```
npm install express
```

---

# 2. CREATE DOCKER CONFIGURATION

## 2.1 Create Dockerfile
```
touch Dockerfile
```

## 2.2 Add Dockerfile content
```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json .
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "app.js"]
```

## 2.3 Build docker image locally
```
docker build -t myapp .
```

## 2.4 Run locally to test
```
docker run -p 3000:3000 myapp
```

Open browser:  
👉 http://localhost:3000

---

# 3. CREATE docker-compose.yml

## 3.1 Create file
```
touch docker-compose.yml
```

## 3.2 Add content
```yaml
version: "3.8"
services:
  app:
    build: .
    ports:
      - "3000:3000"
```

## 3.3 Run with compose
```
docker-compose up --build
```

---

# 4. PUSH CODE TO GITHUB

## 4.1 Initialize git
```
git init
git add .
git commit -m "initial commit"
```

## 4.2 Create GitHub repo
Go to GitHub → New Repository → Name it: **ci-cd-demo**

## 4.3 Push project
```
git remote add origin https://github.com/mullafurqan/ci-cd-demo.git
git push -u origin main
```

---

# 5. SET UP GITHUB ACTIONS

## 5.1 Create workflow directory
```
mkdir -p .github/workflows
```

## 5.2 Create CI/CD file
```
touch .github/workflows/ci-cd.yml
```

## 5.3 Add workflow content
```yaml
name: CI/CD Pipeline

on:
  push:
    branches: ["main"]

jobs:

  build-test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 18

      - name: Install dependencies
        run: npm install

      - name: Test build
        run: echo "No tests yet"

  docker:
    needs: build-test
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and Push Docker Image
        uses: docker/build-push-action@v6
        with:
          push: true
          tags: ${{ secrets.DOCKER_USERNAME }}/ci-cd-demo:latest
```

---

# 6. SETUP GITHUB SECRETS

Go to:

**GitHub → Repository → Settings → Secrets & Variables → Actions**

Add:

| Secret Key        | Value |
|-------------------|--------|
| DOCKER_USERNAME   | your Docker Hub username |
| DOCKER_PASSWORD   | your Docker Hub password/token |

Required for pushing Docker images.

---

# 7. DOCKER HUB SETUP

## 7.1 Login to Docker Hub  
https://hub.docker.com

## 7.2 Create repository  
Name → **ci-cd-demo**

Your image link will be:

```
docker.io/mullafurqan/ci-cd-demo:latest
```

---

# 8. PUSH CODE TO TRIGGER CI/CD

## 8.1 Commit
```
git add .
git commit -m "added CI/CD pipeline"
git push
```

## 8.2 Check workflow  
GitHub → Actions → View results

---

# 9. DEPLOY ON MINIKUBE (LOCAL)

## 9.1 Start Minikube
```
minikube start
```

## 9.2 Create Kubernetes deployment file
```
touch k8s-deployment.yaml
```

## 9.3 Add Kubernetes manifest
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
        - name: myapp
          image: mullafurqan/ci-cd-demo:latest
          ports:
            - containerPort: 3000
---
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  selector:
    app: myapp
  ports:
    - port: 3000
      targetPort: 3000
      nodePort: 32000
```

## 9.4 Apply deployment
```
kubectl apply -f k8s-deployment.yaml
```

## 9.5 Check pods
```
kubectl get pods
```

## 9.6 Access the app
```
minikube service myapp-service
```

Or manually:
```
http://$(minikube ip):32000
```

---

# 10. DELIVERABLES (FOR SUBMISSION)

## 10.1 GitHub Repository Link  
Includes:
✔ Source code  
✔ Dockerfile  
✔ Docker Compose  
✔ GitHub Actions workflow  
✔ Kubernetes files  

## 10.2 Docker Hub Image Link  
Example:  
https://hub.docker.com/r/mullafurqan/ci-cd-demo

## 10.3 CI/CD Workflow Screenshot  
From GitHub Actions

## 10.4 Minikube Deployment Screenshot  
- Pod running  
- Service URL  
- Browser app running  

---
```

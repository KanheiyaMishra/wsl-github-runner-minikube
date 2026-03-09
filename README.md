Minikube CI/CD Demo with GitHub Actions (WSL Self‑Hosted Runner)
This repository demonstrates how to run a self‑hosted GitHub Actions runner inside WSL2 and use it to deploy a simple Node.js app into a Minikube Kubernetes cluster.
It’s designed as a teaching/demo project for CI/CD pipelines targeting local Kubernetes.

📖 Project Overview
App: Minimal Node.js Express server (Hello from Minikube CI/CD Test App!)

Containerization: Dockerfile builds the app image

Cluster: Minikube running inside WSL2 with Docker driver

CI/CD: GitHub Actions workflow builds image, applies Kubernetes manifests, and verifies deployment

Runner: Self‑hosted GitHub Actions runner inside WSL2

🛠️ Prerequisites
Windows 10/11 with WSL2 enabled

Ubuntu (recommended) as WSL distro

Docker installed inside WSL

GitHub repository (this one cloned/forked)

Minikube + kubectl installed in WSL

⚙️ Setup Instructions
1. Prepare WSL
bash
sudo apt-get update && sudo apt-get upgrade -y
sudo apt-get install -y docker.io
sudo usermod -aG docker $USER
newgrp docker
2. Install Minikube + kubectl
bash
sudo apt-get install -y conntrack socat

# Minikube
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
chmod +x minikube
sudo mv minikube /usr/local/bin/

# kubectl
curl -LO "https://dl.k8s.io/release/$(curl -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
Start Minikube:

bash
minikube start --driver=docker
kubectl cluster-info
3. Install GitHub Runner
Go to GitHub → Repo → Settings → Actions → Runners → New self‑hosted runner

Copy registration token + commands

Inside WSL:

bash
mkdir actions-runner && cd actions-runner
curl -o actions-runner-linux-x64.tar.gz -L https://github.com/actions/runner/releases/latest/download/actions-runner-linux-x64.tar.gz
tar xzf ./actions-runner-linux-x64.tar.gz
./config.sh --url https://github.com/<your-username>/<your-repo> --token <registration-token>
./run.sh
📂 Project Structure
Code
test-app/
├── app/
│   ├── index.js
│   ├── package.json
│   └── Dockerfile
├── k8s/
│   ├── deployment.yaml
│   └── service.yaml
└── .github/workflows/deploy.yml
🚀 Workflow Explanation
The GitHub Actions workflow (deploy.yml) does the following:

Checkout code

Build Docker image inside Minikube

bash
eval $(minikube docker-env)
docker build -t test-app:latest ./app
Deploy manifests (kubectl apply)

Verify pods (kubectl get pods)

Smoke test: port‑forward service → curl endpoint → confirm response

🔄 Demo Flow
Push code → GitHub triggers workflow

WSL self‑hosted runner executes job

Docker image built inside Minikube

Kubernetes manifests applied → pods + service created

Workflow verifies pods and tests app response

Access app via:

bash
minikube service test-app-service
or directly at http://<minikube-ip>:30080

🎥 Suggested Video Plan
Intro: Explain why self‑hosted runner in WSL is better than cloud runner for Minikube

Setup: Show installation commands (WSL, Docker, Minikube, runner)

Code Walkthrough: Show app + manifests + workflow YAML

Demo: Push code → watch GitHub Actions run → show pods → open app in browser

Outro: Highlight persistence of cluster and next step (transition to cloud Kubernetes)

🧹 Cleanup
To remove resources after demo:

bash
kubectl delete -f k8s/deployment.yaml
kubectl delete -f k8s/service.yaml
This README gives your repo a professional, educational structure and doubles as a script for your YouTube episode.

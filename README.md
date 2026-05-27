# devops-infra

Jenkins CI/CD infrastructure with Docker agents and Traefik SSL.

## Structure
```

devops-infra/
├── docker-compose.yml      — Jenkins Master
├── jenkins/Dockerfile      — Agent image
├── pipelines/Jenkinsfile   — Default CI pipeline
├── .env.example            — Environment template
└── README.md
```


## Requirements
- Server with Docker installed
- Traefik running (see server-infra repo)
- DNS wildcard A record pointing to server IP

## Setup

### 1. Clone
```bash
git clone https://github.com/matanlahmi/devops-infra.git
cd devops-infra
cp .env.example .env
nano .env
```

### 2. Build Agent Image
```bash
docker build --build-arg DOCKER_GID=$(stat -c '%g' /var/run/docker.sock) \
  -t matanlahmi/jenkins-agent:latest ./jenkins/
docker push matanlahmi/jenkins-agent:latest
```

### 3. Start Jenkins
```bash
docker compose up -d
```

### 4. Get initial password
```bash
docker exec jenkins-master cat /var/jenkins_home/secrets/initialAdminPassword
```

### 5. Access
https://jenkins.YOUR_DOMAIN

## Jenkins Manual Setup (one time)
1. Install plugins: Docker Plugin, Git Plugin, Pipeline, Credentials Binding
2. Manage Jenkins → Nodes → Built-In Node → Executors → 0
3. Manage Jenkins → Clouds → New Cloud → Docker
   - URI: unix:///var/run/docker.sock
   - Agent image: matanlahmi/jenkins-agent:latest
   - Label: docker-agent
4. Add Docker Hub credentials (ID: docker-hub-credentials)

## Default CI Pipeline
Checkout → Flake8 + pip-audit (parallel) → pytest → Docker Build → Trivy → Push



# 🔧 DevOps Planning: Microservices Pipeline for Django + Next.js

## 📦 Technology Stack

- **Frontend**: Next.js (React-based)
- **Backend**: Django (REST API)
- **Database**: Azure PostgreSQL
- **CI/CD**: GitHub Actions (Primary), GitLab CI/CD, Jenkins
- **Containerization**: Docker
- **Artifact Repository**: JFrog Artifactory / Nexus
- **Code Quality**: SonarQube
- **Deployment**: Docker Compose on Azure VM / AWS EC2

---

## 🧱 Architecture Overview

```
           +-----------------------------+
           |       Developers            |
           |  (Frontend & Backend Devs)  |
           +-------------+---------------+
                         |
                GitHub (2 Repositories)
            (frontend repo + backend repo)
                         |
              +----------+------------+
              |      CI/CD Pipeline   |
              +----------+------------+
                         |
         +---------------+------------------+
         |               |                  |
   Code Quality     Unit Tests       Build Docker Images
     (SonarQube)    (Jest, Pytest)   (Backend & Frontend)
         |               |                  |
         +--------+------+------------------+
                  |
           Store Artifacts (Nexus/JFrog)
                  |
           +------+--------+
           | Remote VMs / EC2|
           |   (Docker Compose) |
           +------------------+
                  |
      +-----------+------------+
      |                        |
Docker Backend          Docker Frontend
(Django + .env)         (Next.js + .env)
```

---

## 📁 Repo Structure

```
github.com/<org>/glynac-backend/
github.com/<org>/Extraction_app/
```

Each repository has:
- Its own `.env`, `Dockerfile`, and workflows
- Built independently and deployed via Compose

---

## ⚙️ Docker Compose

```yaml
version: "3.9"
services:
  backend:
    image: yourdockerhub/glynac-backend:latest
    env_file:
      - ./backend.env
    ports:
      - "8000:8000"

  frontend:
    image: yourdockerhub/Extraction_app:latest
    env_file:
      - ./frontend.env
    ports:
      - "3000:3000"
```

---

## 🛠 .env (Backend Example)

```
DATABASE_URL=postgresql://<username>:pwd@server/db_name?sslmode=require
```

---

## 🔁 CI/CD Options

### ✅ Option 1: GitHub Actions (Recommended for GitHub Repos)

- Native GitHub integration
- No separate CI server required
- Uses workflows in `.github/workflows/`

### ⚙️ Option 2: GitLab CI/CD

- If code is in GitLab instead of GitHub
- `.gitlab-ci.yml` file controls the pipeline
- Supports custom runners, pipelines, and jobs

### ⚙️ Option 3: Jenkins (Advanced)

- Full control over jobs and pipelines
- Can integrate with both GitHub and GitLab
- Hosted on a VM or EC2 instance

> Recommendation: Start with GitHub Actions; move to Jenkins if you need complex workflows or host multiple apps.

---

## 🧪 Testing & Quality Gates

- **Frontend**: `Jest`, `ESLint`, `Prettier`
- **Backend**: `pytest`, `coverage.py`, `flake8`
- **SonarQube**: Code scanning via CLI or plugins

---

## 📦 Artifact Repository

- Docker images pushed to Nexus or JFrog
- Versioned by tags (e.g., v1.0.0, latest)
- Downloadable from CI or manually

---

## 🚀 Deployment Options

- **Option 1**: Docker Compose on **AWS EC2**
- **Option 2**: Docker Compose on **Azure VM (Ubuntu)**


AWS EC2 VM Deployment Steps:
1. Launch EC2 (Ubuntu) with ports 22, 8000, 3000 open.
2. SSH into the server.
3. Pull images:
```bash
docker pull yourdockerhub/glynac-backend
docker pull yourdockerhub/Extraction_app
```
4. Prepare `.env` files (`backend.env`, `frontend.env`)
5. Create and run `docker-compose.yml`:
```bash
docker compose up -d
```

Azure VM Deployment Steps:
1. Create Ubuntu VM on Azure and open ports 22, 8000, 3000.
2. SSH into VM and install Docker, Docker Compose.
3. Pull Docker images from Docker Hub:
   ```bash
   docker pull yourdockerhub/glynac-backend
   docker pull yourdockerhub/glynac-frontend
   ```
4. Setup `.env` and `docker-compose.yml`, then run:
   ```bash
   docker compose up -d
   ```

---

## 📈 Monitoring & Logging

- Use `docker logs <container>` for logs
- Optional: Prometheus + Grafana stack

---

## 🔐 Future Plans

### 🔒 Security & Secrets Management
- Use **Azure Key Vault** or **AWS Secrets Manager** for managing DB credentials, API keys.
- Store only environment-safe data in `.env` during local development.

### ✅ Quality Gates
- Integrate **SonarQube** for static code analysis and test coverage.
- Extend pipeline to block merges on low quality score.

### 📊 Observability (Optional Enhancements)
- Add **Grafana + Prometheus** for container resource monitoring.
- Use **ELK Stack** or **Fluentd** for centralized logging.

---

## ✅ Summary

Now we have a full DevOps pipeline that supports:
- Separate repositories for frontend and backend
- Clean CI/CD integration with GitHub Actions, GitLab, or Jenkins
- Secure remote PostgreSQL usage
- Docker Compose for lightweight orchestration and deployment in cloud.
- Artifact storage, quality checks, secrets management planned


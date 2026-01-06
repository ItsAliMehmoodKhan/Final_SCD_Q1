
* Containerize all microservices
* Run the system in an isolated and reproducible environment
* Enable inter-service communication through an API Gateway
* Automate the build and delivery process using GitHub Actions
* Push Docker images to a container registry


## Step 1: Clone the Repository

```bash
git clone https://github.com/ItsAliMehmoodKhan/Final_SCD_Q1.git
cd Final_SCD_Q1
```

---

## Step 2: Verify Services Locally (Optional but Recommended)

Before containerization, each service should be verified individually.

```bash
cd auth-service
npm install
npm start
```

Repeat this process for each service to ensure they work correctly.

---

## Step 3: Dockerizing Each Microservice

Each service contains its own `Dockerfile`. A typical Dockerfile for a Node.js service is shown below:

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install --production

COPY . .

EXPOSE 4001

CMD ["node", "server.js"]
```

The Dockerfile:

* Uses a lightweight Node.js base image
* Installs dependencies
* Copies source code
* Exposes the service port
* Starts the application

Each service has its own port and startup command as required.

---

## Step 4: Orchestrating Services Using Docker Compose

A `docker-compose.yml` file is used to start all services together in an isolated network.

```yaml
version: "3.8"

services:
  api-gateway:
    build: ./api-gateway
    ports:
      - "4000:4000"
    environment:
      - AUTH_SERVICE_URL=http://auth-service:4001
    depends_on:
      - auth-service

  auth-service:
    build: ./auth-service
    environment:
      - PORT=4001
```



## Step 5: API Gateway Configuration

The API Gateway forwards incoming requests to internal services. Inside the gateway code, service URLs are defined using environment variables:

```js
const AUTH_SERVICE_URL = process.env.AUTH_SERVICE_URL || "http://auth-service:4001";
```

---

## Step 10: GitHub Actions Workflow

A GitHub Actions workflow automates the Docker build and push process.

File location:

```
.github/workflows/docker-build.yml
```

Workflow behavior:

* Triggers on every push to the main branch
* Builds Docker images for all services
* Pushes images to GitHub Container Registry

The workflow uses a matrix strategy to build all services in parallel.

---

## Step 11: Triggering the CI/CD Pipeline

After configuring the workflow:

```bash
git add .
git commit -m "Add Docker CI/CD pipeline"
git push origin main
```

GitHub Actions automatically executes the pipeline.

---

## Step 12: Verifying Docker Images

After the workflow completes:

1. Go to the GitHub repository
2. Open the Packages section
3. Verify that Docker images for each service are available

Images can also be pulled locally:

```bash
docker pull ghcr.io/<owner>/<image-name>:latest
```

---

## Conclusion

This project demonstrates a complete microservices deployment pipeline using Docker and GitHub Actions. The system is:

* Fully containerized
* Reproducible across environments
* Isolated using Docker networking
* Automated through CI/CD pipelines

This setup satisfies modern software engineering and distributed systems requirements and is suitable for academic and professional use.

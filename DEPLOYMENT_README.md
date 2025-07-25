# Todo List Node.js - DevOps Setup

This repository contains a Dockerized Node.js Todo List application with GitHub Actions CI/CD pipeline.

## Project Structure
- **Node.js Application**: Express.js server with MongoDB database
- **Docker**: Containerized application with multi-stage build
- **GitHub Actions**: Automated CI/CD pipeline for building and pushing images

## Prerequisites
- Node.js 18+
- Docker
- MongoDB (for local development)
- GitHub account
- Docker Hub account or private registry access

## Local Development

### 1. Environment Setup
Create a `.env` file with your MongoDB connection string:
```
mongoDbUrl = mongodb://localhost:27017/todolistdb
```

### 2. Install Dependencies
```bash
npm install
```

### 3. Run the Application
```bash
npm start
```
The application will be available at `http://localhost:4000`

## Docker Deployment

### 1. Build Docker Image
```bash
docker build -t todo-list-nodejs .
```

### 2. Run with Docker Compose (includes MongoDB)
```bash
docker-compose up -d
```

### 3. Run Single Container (requires external MongoDB)
```bash
docker run -p 4000:4000 -e mongoDbUrl="your-mongodb-connection-string" todo-list-nodejs
```

## GitHub Actions CI/CD Pipeline

### Setup Instructions

#### For Docker Hub Registry:
1. Go to your GitHub repository settings
2. Navigate to Secrets and Variables > Actions
3. Add the following secrets:
   - `DOCKER_USERNAME`: Your Docker Hub username
   - `DOCKER_PASSWORD`: Your Docker Hub password or access token

#### For Private Registry (AWS ECR):
1. Add the following secrets:
   - `AWS_ACCESS_KEY_ID`: Your AWS access key
   - `AWS_SECRET_ACCESS_KEY`: Your AWS secret key
2. Update the region in the workflow file if needed

#### For Generic Private Registry:
1. Uncomment the private registry login section in `ci-cd-private-registry.yml`
2. Add the following secrets:
   - `REGISTRY_USERNAME`: Your registry username
   - `REGISTRY_PASSWORD`: Your registry password
3. Update the `REGISTRY` environment variable with your registry URL

### Workflow Features
- **Automated Testing**: Runs npm tests (if available)
- **Multi-platform Builds**: Supports both AMD64 and ARM64 architectures
- **Caching**: Uses GitHub Actions cache for faster builds
- **Metadata Extraction**: Automatically tags images with branch names, PR numbers, and commit SHAs
- **Security**: Uses secrets for sensitive information

### Triggered Events
- Push to `main` or `master` branch
- Pull requests to `main` or `master` branch

## Environment Variables
- `mongoDbUrl`: MongoDB connection string

## Ports
- Application: `4000`
- MongoDB: `27017`

## Security Considerations
- Non-root user in Docker container
- Environment variables for sensitive configuration
- GitHub Secrets for registry credentials
- .dockerignore to exclude sensitive files

## Monitoring and Logs
- Application logs are available via `docker logs <container-name>`
- Health check endpoint can be added for monitoring

## Troubleshooting

### Common Issues
1. **MongoDB Connection**: Ensure MongoDB is running and connection string is correct
2. **Port Conflicts**: Make sure port 4000 is not in use
3. **Docker Build Fails**: Check Dockerfile syntax and dependencies
4. **CI/CD Fails**: Verify GitHub secrets are correctly configured

### Local Testing
Test the Docker build locally before pushing:
```bash
docker build -t todo-list-nodejs .
docker run -p 4000:4000 todo-list-nodejs
```

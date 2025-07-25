# GitHub Actions CI/CD Pipeline Setup Guide

This guide explains how to set up the GitHub Actions CI/CD pipeline to build and push your Docker image to various private registries.

## 🔧 Pipeline Features

- **Automated Testing**: Runs npm tests before building
- **Multi-platform Builds**: Supports both AMD64 and ARM64 architectures  
- **Smart Caching**: Uses GitHub Actions cache for faster builds
- **Multiple Triggers**: Runs on push to main/master and pull requests
- **Flexible Tagging**: Auto-tags with branch names, commit SHAs, and latest

## 📋 Setup Instructions

### Option 1: Docker Hub Private Repository

1. **Create Docker Hub Repository**:
   - Go to [Docker Hub](https://hub.docker.com)
   - Create a new private repository named `todo-list-nodejs`

2. **Configure GitHub Secrets**:
   - Go to your GitHub repository → Settings → Secrets and Variables → Actions
   - Add these secrets:
     ```
     DOCKER_USERNAME: your-dockerhub-username
     DOCKER_PASSWORD: your-dockerhub-password-or-token
     ```

3. **Use the workflow**: The `ci-cd.yml` file is already configured for Docker Hub

### Option 2: AWS ECR (Elastic Container Registry)

1. **Create ECR Repository**:
   ```bash
   aws ecr create-repository --repository-name todo-list-nodejs --region us-east-1
   ```

2. **Configure GitHub Secrets**:
   ```
   AWS_ACCESS_KEY_ID: your-aws-access-key
   AWS_SECRET_ACCESS_KEY: your-aws-secret-key
   ```

3. **Update the workflow**:
   - Rename `ci-cd-private-registry.yml` to `ci-cd.yml`
   - Update the region in the workflow file if needed

### Option 3: Azure Container Registry (ACR)

1. **Create Azure Container Registry**:
   ```bash
   az acr create --resource-group myResourceGroup --name myregistry --sku Basic
   ```

2. **Create the workflow file**:
   ```yaml
   # Add to .github/workflows/ci-cd-azure.yml
   - name: Login to Azure Container Registry
     uses: azure/docker-login@v1
     with:
       login-server: myregistry.azurecr.io
       username: ${{ secrets.REGISTRY_USERNAME }}
       password: ${{ secrets.REGISTRY_PASSWORD }}
   ```

3. **Configure GitHub Secrets**:
   ```
   REGISTRY_USERNAME: your-acr-username
   REGISTRY_PASSWORD: your-acr-password
   ```

### Option 4: Google Container Registry (GCR)

1. **Setup Service Account**:
   ```bash
   gcloud iam service-accounts create github-actions
   gcloud projects add-iam-policy-binding PROJECT_ID \
     --member="serviceAccount:github-actions@PROJECT_ID.iam.gserviceaccount.com" \
     --role="roles/storage.admin"
   ```

2. **Configure GitHub Secrets**:
   ```
   GCP_SA_KEY: base64-encoded-service-account-key
   GCP_PROJECT_ID: your-gcp-project-id
   ```

## 🚀 Workflow Execution

The pipeline will automatically:

1. **Trigger** on:
   - Push to `main` or `master` branch
   - Pull requests to `main` or `master`

2. **Execute steps**:
   - Checkout code
   - Setup Node.js environment
   - Install dependencies
   - Run tests (if available)
   - Setup Docker Buildx
   - Login to registry
   - Build multi-platform Docker image
   - Push to registry with smart tags

3. **Generate tags**:
   - `latest` (for main branch)
   - `main-<commit-sha>` (for main branch commits)
   - `pr-<pr-number>` (for pull requests)

## 🏷️ Image Tags Generated

- **Latest**: `your-registry/todo-list-nodejs:latest`
- **Branch**: `your-registry/todo-list-nodejs:main`
- **Commit**: `your-registry/todo-list-nodejs:main-abc1234`
- **PR**: `your-registry/todo-list-nodejs:pr-123`

## 🔒 Security Best Practices

1. **Use Personal Access Tokens** instead of passwords
2. **Limit token permissions** to only what's needed
3. **Use repository secrets** for sensitive information
4. **Enable dependency scanning** in GitHub
5. **Use signed commits** for additional security

## 🐛 Troubleshooting

### Common Issues:

1. **Authentication Failed**:
   - Verify secrets are correctly set
   - Check token permissions
   - Ensure registry URL is correct

2. **Build Failed**:
   - Check Dockerfile syntax
   - Verify all dependencies are available
   - Review build logs for specific errors

3. **Push Failed**:
   - Confirm registry permissions
   - Check repository exists
   - Verify image size limits

### Testing Locally:

```bash
# Test Docker build
docker build -t todo-list-nodejs .

# Test with the same platforms as CI
docker buildx build --platform linux/amd64,linux/arm64 -t todo-list-nodejs .

# Test the application
docker run -p 4000:4000 todo-list-nodejs
```

## 📊 Monitoring

After setup, you can monitor your pipeline:

1. **GitHub Actions tab**: View workflow runs
2. **Registry dashboard**: Monitor image pulls/pushes  
3. **Notifications**: Set up alerts for failed builds

## 🔄 Next Steps

1. Set up staging/production environments
2. Add integration tests
3. Implement semantic versioning
4. Add security scanning
5. Set up automated deployments

---

**Note**: Replace placeholder values (like `your-registry.com`, `PROJECT_ID`, etc.) with your actual values before using the workflows.

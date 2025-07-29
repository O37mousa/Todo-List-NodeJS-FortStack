# Todo List Node.js Application

This is a full-stack Node.js application that allows users to register, log in, and manage their daily tasks. It connects to a MongoDB Atlas database and includes Docker, GitHub Actions for CI/CD, and an Ansible-based deployment pipeline.

---

## Features

- User authentication (register/login/logout)
- Task creation, completion, and deletion
- Responsive UI built with EJS and vanilla CSS
- MongoDB Atlas for remote database storage
- Dockerized application
- CI/CD with GitHub Actions and private Docker Hub repository
- Automated provisioning with Ansible
- Auto-update deployment using Watchtower

---

## How to Run Locally

### Prerequisites

- [Node.js](https://nodejs.org/)
- [Docker](https://www.docker.com/)
- [MongoDB Atlas account](https://www.mongodb.com/cloud/atlas)

### Steps

1. **Clone the Repository:**

```bash
git clone https://github.com/yourusername/Todo-List-nodejs.git
cd Todo-List-nodejs
```

2. **Configure Environment Variables:**

```bash
cp .env.example .env
```

Edit the `.env` file and add your MongoDB Atlas connection string:

```
mongoDbUrl=mongodb+srv://your_user:your_password@cluster.mongodb.net/?retryWrites=true&w=majority
```

3. **Run with Docker Compose:**

```bash
docker compose up -d
```

4. **Access the App:** Visit [http://localhost:4001](http://localhost:4001)

---

## CI/CD Pipeline with GitHub Actions

### Workflow File: `.github/workflows/docker-image.yml`

- Trigger: On push to `main`
- Builds the Docker image
- Pushes it to your Docker Hub private repo

Make sure to configure your repository secrets:

- `DOCKER_USERNAME`
- `DOCKER_PASSWORD`

---

## Deployment with Ansible

### Requirements:

- A CentOS-based VM
- Public IP address
- SSH access using a private key

### Steps:

1. Add the VM to your `hosts` file:

```ini
[web]
192.168.1.12 ansible_user=osboxes ansible_ssh_private_key_file=~/.ssh/id_rsa
```

2. Run the playbook:

```bash
ansible-playbook -i hosts install_docker.yml --ask-become-pass
```

3. On the VM, create a directory:

```bash
mkdir ~/todo-deploy && cd ~/todo-deploy
```

4. Copy your `docker-compose.yml` to this folder and run:

```bash
sudo docker compose up -d
```

---

## Auto Updates with Watchtower

Watchtower monitors your Docker containers and auto-pulls new images from Docker Hub.

Run it on the VM:

```bash
sudo docker run -d \
  --name watchtower \
  --restart unless-stopped \
  -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower
```

---

## 🛠️ Modified Files Overview

This section documents all custom or modified files in this project and their roles in the development, deployment, and automation of the application.

| File                        | Purpose                                                                                                                 |
| --------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `.env.example`            | Template for required environment variables. Developers should copy this to `.env` and fill in their MongoDB URL.     |
| `.gitignore`              | Modified to exclude `.env` and other sensitive or environment-specific files from version control.                    |
| `Dockerfile`              | Dockerizes the Node.js application. Uses `node:18`, installs dependencies, and configures the app with `nodemon`.   |
| `docker-compose.yml`      | Defines the service container and injects environment variables (like `mongoDbUrl`) using the `environment:` field. |
| `.github/workflows/*.yml` | GitHub Actions workflow that builds and pushes the Docker image to Docker Hub when you push to `main`.                |
| `install_docker.yml`      | Ansible playbook to install Docker on a CentOS VM. Used to prepare the environment before deployment.                   |
| `hosts`                   | Ansible inventory file specifying the target CentOS VM and connection settings for the deployment automation.           |
| `DEPLOYMENT_README.md`    | Optional file to hold internal deployment instructions or notes.                                                        |
| `README.md`               | The main project documentation, including setup instructions, deployment steps, and technical notes for new developers. |

---

## Project Tasks (Intern Assessment)

### Part 1 (30 points):

- Cloned and customized `Todo-List-nodejs` app.
- Dockerized the application.
- Used GitHub Actions to build and push to **private** Docker Hub registry.

### Part 2 (30 points):

- Created a local CentOS VM.
- Used Ansible from host to install Docker on the VM.

### Part 3 (40 points):

- Used `docker-compose` on VM to run the application.
- Used Watchtower for automatic updates when a new image is pushed.

---

## Author

**Omar Mousa** — [GitHub: o37mousa](https://github.com/o37mousa)

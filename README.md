# AWS DevOps Pipelines

Welcome to the AWS DevOps Pipelines repository! This repository showcases various projects and scripts related to AWS DevOps, including CI/CD pipeline setups, infrastructure as code with Terraform, containerization with Docker, and deployment automation with Jenkins.

## Repository Contents

1. **Jenkins Pipeline Script**: 
    - A Jenkins pipeline script for automating the build, test, and deployment process of frontend and backend applications.
    - Supports both Angular (frontend) and Java (backend) applications.
    - Includes steps for checking out code from GitHub, building Docker images, pushing them to Docker Hub, and deploying to a production servers


## Jenkins Pipeline Script Details

The Jenkins pipeline script (`Jenkinsfile`) in this repository demonstrates how to automate the CI/CD process for a project that includes both frontend and backend components. Here is an overview of the script:

### Parameters

- `BRANCH`: Select the branch to deploy (e.g., main, develop, feature-develop).
- `CONTAINERS`: Select the container to deploy (FRONTEND_IMAGE, BACKEND_IMAGE).

### Environment Variables

- `DOCKER_REGISTRY`: Docker registry URL.
- `FRONTEND_IMAGE`: Docker image name for the frontend application.
- `BACKEND_IMAGE`: Docker image name for the backend application.
- `FRONTEND_REPO`: GitHub repository URL for the frontend application.
- `BACKEND_REPO`: GitHub repository URL for the backend application.
- `DOCKERHUB_CREDENTIALS`: Docker Hub credentials ID in Jenkins.
- `GIT_CREDENTIALS`: GitHub credentials ID in Jenkins.
- `PROD_SERVER`: Production server IP address.
- `PROD_USER`: Production server user.
- `PROD_SSH_CREDENTIALS`: SSH credentials ID for production server in Jenkins.

### Stages

1. **Checkout**:
    - Clones the specified branch from the selected repository (frontend or backend).

2. **Build JAR File** (for backend only):
    - Builds the backend application using Maven.

3. **Build Docker Image**:
    - Builds the Docker image for the selected application (frontend or backend).

4. **Push to Registry**:
    - Pushes the built Docker image to Docker Hub.

5. **Deploy to Production**:
    - Deploys the Docker image to the production server.
    - Pulls the latest Docker image, stops the existing container, removes it, and runs a new container.

## How to Use

1. **Clone the repository**:
    ```sh
    git clone https://github.com/your-username/aws-devops-pipelines.git
    ```

2. **Navigate to the repository directory**:
    ```sh
    cd aws-devops-pipelines
    ```

3. **Set up your Jenkins environment**:
    - Add credentials for Docker Hub and GitHub.
    - Add SSH credentials for your production server.

4. **Configure the Jenkins pipeline**:
    - Create a new Jenkins pipeline job.
    - Use the `Jenkinsfile` from this repository.

5. **Run the pipeline**:
    - Select the branch and container type (frontend/backend) to deploy.
    - Monitor the pipeline execution and check for any issues.

Happy DevOps-ing! 🚀
# automation-scripts

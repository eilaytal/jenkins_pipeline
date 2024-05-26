# Jenkins Pipeline for Weather App with GitOps

This repository contains a Jenkins pipeline script (`Jenkinsfile`) for automating the build, testing, versioning, and deployment of the Weather App. It also utilizes GitOps with Argo CD for managing Kubernetes manifests.

## Overview

The Jenkins pipeline is designed to automate the following tasks:

1. **Docker Build Stage:** Builds the Docker image for the Weather App.
2. **Run Tests Stage:** Runs tests on the Docker container to ensure its functionality.
3. **Push Stage:** Pushes the Docker image to Docker Hub with a version tag.
4. **Clone and Update GIT Stage:** Clones a Git repository, updates a specific file with the new version tag, commits the changes, and pushes them back to the repository.
5. **Post-Build Actions:** Stops and removes the Docker container used for testing and sends notifications to Slack channels based on the build status.

In addition to Jenkins, this pipeline integrates with Argo CD for GitOps-style deployment of Kubernetes manifests stored in the `gitops-manifests` repository.

## Prerequisites

Before using the Jenkins pipeline and Argo CD, ensure you have the following:

- Jenkins installed and configured with necessary plugins.
- Docker installed on the Jenkins server.
- Access to Docker Hub for pushing Docker images.
- Access to a Git repository for updating version tags.
- Argo CD installed and configured in your Kubernetes cluster.
- A Kubernetes cluster configured with Argo CD to manage deployments.

## Usage

1. **Create a Jenkins Pipeline Job:** Create a new pipeline job in Jenkins and configure it to use this repository as the source.

2. **Set up Credentials:** Configure Jenkins credentials for Docker Hub and the Git repository. Use the credentials IDs (`DockerHub`, `github`) in the Jenkinsfile.

3. **Run the Pipeline:** Trigger the pipeline manually or set up webhooks to trigger it automatically on code changes.

4. **GitOps Deployment:** Utilize Argo CD to manage deployments of Kubernetes manifests stored in the `gitops-manifests` repository. Ensure Argo CD is set up to monitor changes in the `gitops-manifests` repository and automatically sync deployments in the Kubernetes cluster.

## How it Works

- The pipeline script (`Jenkinsfile`) defines stages for building Docker images, running tests, pushing images to Docker Hub, and updating version tags in a Git repository.
- Argo CD continuously monitors the `gitops-manifests` repository for changes and automatically applies any modifications to the Kubernetes cluster, ensuring consistent and automated deployments.

## Contributing

If you find any issues or have suggestions for improvements, feel free to open an issue or create a pull request.

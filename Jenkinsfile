pipeline {
    agent any
    
    environment {
        // Docker image names
        FRONTEND_IMAGE = 'travelmemory-frontend'
        BACKEND_IMAGE = 'travelmemory-backend'
        REGISTRY = 'docker.io'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/Shrutika-Panchal/TM-Docker.git'
            }
        }

        stage('Get Git Commit Hash') {
            steps {
                script {
                    // Fetch the latest Git commit hash
                    env.GIT_COMMIT_HASH = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                    echo "Git Commit Hash: ${env.GIT_COMMIT_HASH}"
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    // Build Docker images using Git commit hash as tag
                    sh "docker build -t ${REGISTRY}/${DOCKERHUB_USERNAME}/${FRONTEND_IMAGE}:${env.GIT_COMMIT_HASH} ./frontend"
                    sh "docker build -t ${REGISTRY}/${DOCKERHUB_USERNAME}/${BACKEND_IMAGE}:${env.GIT_COMMIT_HASH} ./backend"
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                // Use credentials binding to inject Docker Hub credentials
                withCredentials([usernamePassword(credentialsId: 'Docker', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    // Docker login command using credentials
                    sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    // Push the Docker images to Docker Hub with the Git commit hash tag
                    sh "docker push ${REGISTRY}/${DOCKERHUB_USERNAME}/${FRONTEND_IMAGE}:${env.GIT_COMMIT_HASH}"
                    sh "docker push ${REGISTRY}/${DOCKERHUB_USERNAME}/${BACKEND_IMAGE}:${env.GIT_COMMIT_HASH}"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy to Kubernetes (minikube) using the deployment and service YAML files
                    sh 'kubectl apply -f ./K8s/frontend-deployment.yaml'
                    sh 'kubectl apply -f ./K8s/frontend-service.yaml'
                    sh 'kubectl apply -f ./K8s/backend-deployment.yaml'
                    sh 'kubectl apply -f ./K8s/backend-service.yaml'
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    // Check the status of the deployments and services
                    sh 'kubectl get pods'
                    sh 'kubectl get svc'
                }
            }
        }
    }

    post {
        always {
            // Clean up any temporary files or containers that were used
            sh 'docker system prune -f'
        }
        success {
            echo "Pipeline succeeded! The app has been successfully deployed to Kubernetes."
        }
        failure {
            echo "Pipeline failed! Please check the logs for more details."
        }
    }
}

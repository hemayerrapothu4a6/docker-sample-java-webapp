pipeline {
    agent any
    environment {
        AWS_REGION = "us-east-1"
        ECR_REGISTRY = "123456789012.dkr.ecr.us-east-1.amazonaws.com"
        IMAGE_NAME = "insurance-app"
        IMAGE_TAG = "${env.BUILD_NUMBER}"
    }
    stages {
        stage('Checkout') {
            steps {
                // Checkout source code containing the Dockerfile
                checkout scm
            }
        }
        stage('Build') {
            steps {
                script {
                    // Build a Docker image using the Docker CLI
                    sh "docker build -t ${ECR_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }
        stage('Push') {
            steps {
                script {
                    // Authenticate with ECR and push the built image
                    sh """
                        aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REGISTRY}
                        docker push ${ECR_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    // Deploy the image to the Docker server as a container
                    sh """
                        docker stop insurance-app-container || true
                        docker rm insurance-app-container || true
                        docker run -d --name insurance-app-container -p 8080:8080 ${ECR_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }
    }
}

pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds' // The ID you will set in Jenkins
        IMAGE_NAME = "your-docker-username/conference-app"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com'
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    // Builds the image using the Dockerfile in your repo
                    sh "docker build -t ${IMAGE_NAME}:${env.BUILD_ID} ."
                    sh "docker tag ${IMAGE_NAME}:${env.BUILD_ID} ${IMAGE_NAME}:latest"
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS}", passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh "echo \$PASS | docker login -u \$USER --password-stdin"
                    sh "docker push ${IMAGE_NAME}:latest"
                }
            }
        }

        stage('Kubernetes Deploy') {
            steps {
                // Deploys to your local K8s cluster (Minikube/Docker Desktop)
                sh "kubectl apply -f k8s-deployment.yaml"
            }
        }
    }
}

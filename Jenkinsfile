pipeline {
    agent any

    environment {
        // Use the exact ID you created in Jenkins Credentials for Docker Hub
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds' 
        IMAGE_NAME = "your-docker-username/conference-app"
    }

    stages {
        // We removed the "Checkout" stage because Jenkins does this automatically 
        // when using "Pipeline script from SCM"

        stage('Docker Build') {
            steps {
                script {
                    // Builds the image using the Dockerfile in your workspace
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
                    sh "docker push ${IMAGE_NAME}:${env.BUILD_ID}"
                }
            }
        }

        stage('Kubernetes Deploy') {
            steps {
                // Ensure kubectl is installed on the Jenkins server
                sh "kubectl apply -f k8s-deployment.yaml"
            }
        }
    }
}

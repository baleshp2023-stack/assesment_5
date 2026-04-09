pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds' 
        // REPLACE 'your-docker-username' with your real username below
        IMAGE_NAME = "baleshp"
    }

    stages {
        stage('Docker Build') {
            steps {
                script {
                    // Changed 'sh' to 'bat' for Windows
                    bat "docker build -t %IMAGE_NAME%:%BUILD_ID% ."
                    bat "docker tag %IMAGE_NAME%:%BUILD_ID% %IMAGE_NAME%:latest"
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS}", passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    // Using batch syntax for credentials and login
                    bat "echo %PASS% | docker login -u %USER% --password-stdin"
                    bat "docker push %IMAGE_NAME%:latest"
                    bat "docker push %IMAGE_NAME%:%BUILD_ID%"
                }
            }
        }

        stage('Kubernetes Deploy') {
            steps {
                // Changed 'sh' to 'bat'
                bat "kubectl apply -f k8s-deployment.yaml"
            }
        }
    }
}

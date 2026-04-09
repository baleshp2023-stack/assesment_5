pipeline {
    agent any

    environment {
        // IDs must match exactly what you created in Jenkins Credentials
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds' 
        K8S_CONFIG_ID = 'k8s-config'
        
        // Your confirmed Docker Hub username
        IMAGE_NAME = "baleshp/conference-app"
    }

    stages {
        stage('Docker Build') {
            steps {
                script {
                    // %BUILD_ID% is a built-in Jenkins variable for the build number
                    bat "docker build -t %IMAGE_NAME%:%BUILD_ID% ."
                    bat "docker tag %IMAGE_NAME%:%BUILD_ID% %IMAGE_NAME%:latest"
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS}", passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    // Logs in and pushes both the specific build number and 'latest' tags
                    bat "echo %PASS% | docker login -u %USER% --password-stdin"
                    bat "docker push %IMAGE_NAME%:latest"
                    bat "docker push %IMAGE_NAME%:%BUILD_ID%"
                }
            }
        }

        stage('Kubernetes Deploy') {
            steps {
                // withCredentials(file: ...) securely handles your uploaded 'config' file
                withCredentials([file(credentialsId: "${K8S_CONFIG_ID}", variable: 'KUBECONFIG_FILE')]) {
                    bat "kubectl --kubeconfig=%KUBECONFIG_FILE% apply -f k8s-deployment.yaml"
                }
            }
        }
    }
}

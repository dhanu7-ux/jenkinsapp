pipeline {
    agent any

    environment {
        IMAGE_NAME = "dhanu197/my-app"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE_NAME}:latest")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-creds') {
                        dockerImage.push("latest")
                        dockerImage.push("build-${BUILD_NUMBER}")
                    }
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    sh 'docker stop my-app-container || true'
                    sh 'docker rm my-app-container || true'
                    sh "docker run -d --name my-app-container -p 3000:3000 ${IMAGE_NAME}:latest"
                }
            }
        }
    }

    post {
        success { echo '✅ Deployed successfully!' }
        failure { echo '❌ Build failed. Check logs.' }
    }
}
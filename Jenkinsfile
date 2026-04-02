pipeline {
    agent any

    environment {
        IMAGE_NAME = "jemi04/my-app"
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
                    bat 'docker stop my-app || exit 0'
                    bat 'docker rm my-app || exit 0'
                    bat 'docker run -d -p 3000:3000 --name my-app jemi04/my-app:latest'
                }
            }
        }

    post {
        success { echo '✅ Deployed successfully!' }
        failure { echo '❌ Build failed. Check logs.' }
    }
}
pipeline {
    agent any
    environment {
        DOCKER_USER = "injeolmi3"
        GIT_REPO_URL = "https://github.com/juliarhm/TP2-Cloud.git"
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: "${GIT_REPO_URL}"
            }
        }
        stage('Build & Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-login', passwordVariable: 'PASS', usernameVariable: 'USER_VAR')]) {
                    // Build image backend dan frontend
                    bat "docker build -t ${DOCKER_USER}/emusic-backend:latest ./backend"
                    bat "docker build -t ${DOCKER_USER}/emusic-frontend:latest ./frontend"
                    
                    // Login ke Docker Hub menggunakan parameter -p (lebih stabil di Windows)
                    bat "docker login -u ${DOCKER_USER} -p ${PASS}"
                    
                    // Push image ke Docker Hub
                    bat "docker push ${DOCKER_USER}/emusic-backend:latest"
                    bat "docker push ${DOCKER_USER}/emusic-frontend:latest"
                }
            }
        }
        stage('Deploy ke Azure AKS') {
            steps {
                withKubeConfig([credentialsId: 'aks-config']) {
                    bat "kubectl apply -f emusic-k8s.yaml"
                    bat "kubectl rollout restart deployment backend-emusic"
                    bat "kubectl rollout restart deployment frontend-emusic"
                }
            }
        }
    }
}
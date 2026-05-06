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
                withCredentials([usernamePassword(credentialsId: 'dockerhub-login', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    // Build image backend dan frontend
                    bat "docker build -t ${USER}/emusic-backend:latest ./backend"
                    bat "docker build -t ${USER}/emusic-frontend:latest ./frontend"
                    
                    bat "echo ${PASS} | docker login -u ${USER} --password-stdin"
                    bat "docker push ${USER}/emusic-backend:latest"
                    bat "docker push ${USER}/emusic-frontend:latest"
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
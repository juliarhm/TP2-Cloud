pipeline {
    agent any
    environment {
        DOCKER_USER = "injeolmi3"
        GIT_REPO_URL = "https://github.com/juliarhm/TP2-Cloud.git"
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: "${https://github.com/juliarhm/TP2-Cloud.git}"
            }
        }
        stage('Build & Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-login', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    // Build image backend dan frontend
                    sh "docker build -t ${USER}/emusic-backend:latest ./backend"
                    sh "docker build -t ${USER}/emusic-frontend:latest ./frontend"
                    
                    sh "echo ${PASS} | docker login -u ${USER} --password-stdin"
                    sh "docker push ${USER}/emusic-backend:latest"
                    sh "docker push ${USER}/emusic-frontend:latest"
                }
            }
        }
        stage('Deploy ke Azure AKS') {
            steps {
                withKubeConfig([credentialsId: 'aks-config']) {
                    sh "kubectl apply -f emusic-k8s.yaml"
                    sh "kubectl rollout restart deployment backend-emusic"
                    sh "kubectl rollout restart deployment frontend-emusic"
                }
            }
        }
    }
}
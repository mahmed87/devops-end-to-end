pipeline {
    agent any
    stages {
        stage('Clone repository') {
            steps {
                git branch: 'selective-push', url: 'https://github.com/mahmed87/devops-end-to-end.git'
            }
        }
        stage('Build Docker images') {
            steps {
                sh 'docker build -t ahmeddihe/devops-end-to-end-backend:latest -f backend/Dockerfile ./backend'
                sh 'docker build -t ahmeddihe/devops-end-to-end-frontend:latest -f frontend/Dockerfile ./frontend'
            }
        }
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-login', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push ahmeddihe/devops-end-to-end-backend:latest'
                    sh 'docker push ahmeddihe/devops-end-to-end-frontend:latest'
                }
            }
        }
        stage('Deploy to Swarm') {
            steps {
                sh 'docker stack deploy --compose-file docker-compose.yml myapp --with-registry-auth'
            }
        }
    }
}


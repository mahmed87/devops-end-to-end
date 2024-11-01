pipeline {
    agent any
    stages {
        stage('Clone repository') {
            steps {
                git branch: 'selective-push',
                    url: 'https://github.com/mahmed87/devops-end-to-end.git',
                    credentialsId: 'github-credentials'  // Matches 'github-credentials' ID
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
                withCredentials([usernamePassword(credentialsId: 'docker-login', 
                                                  usernameVariable: 'DOCKER_USER', 
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push ahmeddihe/devops-end-to-end-backend:latest'
                    sh 'docker push ahmeddihe/devops-end-to-end-frontend:latest'
                }
            }
        }
        stage('Deploy to Swarm') {
            steps {
                sshagent(['jenkins-ssh']) {
		    sh 'ssh -o StrictHostKeyChecking=no ahmed@192.168.0.104 "DOCKER_HOST=unix:///var/run/docker.sock docker stack deploy --compose-file ~/DevOps-End-to-End/docker-compose.yml myapp --with-registry-auth"'
                }
            }
        }
    }
}


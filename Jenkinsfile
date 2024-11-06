pipeline {
    agent any
    
    environment {
        DOCKER_BACKEND_IMAGE = 'ahmeddihe/devops-end-to-end-backend:latest'
        DOCKER_FRONTEND_IMAGE = 'ahmeddihe/devops-end-to-end-frontend:latest'
        DOCKER_REGISTRY = 'DockerHub'  // Updated to match your Docker Hub credentials ID
        GITHUB_REPO = 'https://github.com/mahmed87/devops-end-to-end.git'
        GITHUB_BRANCH = 'main'  // Updated branch to main as 'selective-push' was deleted
        SWARM_HOST = 'SSH' // Updated to use your SSH credentials ID
        DOCKER_COMPOSE_FILE = '~/DevOps-End-to-End/docker-compose.yml'
    }

    stages {
        stage('Clone repository') {
            steps {
                git branch: "${GITHUB_BRANCH}",
                    url: "${GITHUB_REPO}",
                    credentialsId: 'GitHub'  // Updated to match your GitHub credentials ID
            }
        }
        stage('Build Docker images') {
            steps {
                sh "docker build -t ${DOCKER_BACKEND_IMAGE} -f backend/Dockerfile ./backend"
                sh "docker build -t ${DOCKER_FRONTEND_IMAGE} -f frontend/Dockerfile ./frontend"
            }
        }
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_REGISTRY}",
                                                  usernameVariable: 'DOCKER_USER',
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh "docker push ${DOCKER_BACKEND_IMAGE}"
                    sh "docker push ${DOCKER_FRONTEND_IMAGE}"
                }
            }
        }
        stage('Deploy to Swarm') {
            steps {
                sshagent(['SSH']) {  // Updated to match your SSH credentials ID
                    sh "ssh -o StrictHostKeyChecking=no ${SWARM_HOST} 'DOCKER_HOST=unix:///var/run/docker.sock docker stack deploy --compose-file ${DOCKER_COMPOSE_FILE} myapp --with-registry-auth'"
                }
            }
        }
        stage('Post Deployment Verification') {
            steps {
                echo 'Deployment to Docker Swarm completed. Consider adding verification steps here.'
                // Optional: Add commands to check the status of the deployed services
            }
        }
    }
}


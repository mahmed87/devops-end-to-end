pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = 'https://index.docker.io/v1/'
        DOCKER_CREDENTIALS = 'docker-login'
        GITHUB_CREDENTIALS = 'github-credentials-id'
        SWARM_SERVER = '192.168.0.103'
        SWARM_SSH_CREDENTIALS = 'jenkins-ssh'
    }

    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'selective-push')
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: params.BRANCH_NAME,
                    credentialsId: env.GITHUB_CREDENTIALS,
                    url: 'https://github.com/mahmed87/devops-end-to-end.git'
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
                withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDENTIALS,
                                                  usernameVariable: 'DOCKER_USER',
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker tag ahmeddihe/devops-end-to-end-backend:latest ${DOCKER_REGISTRY}ahmeddihe/devops-end-to-end-backend:latest'
                    sh 'docker tag ahmeddihe/devops-end-to-end-frontend:latest ${DOCKER_REGISTRY}ahmeddihe/devops-end-to-end-frontend:latest'
                    sh 'docker push ${DOCKER_REGISTRY}ahmeddihe/devops-end-to-end-backend:latest'
                    sh 'docker push ${DOCKER_REGISTRY}ahmeddihe/devops-end-to-end-frontend:latest'
                }
            }
        }

        stage('Deploy to Swarm') {
            steps {
                sshagent([env.SWARM_SSH_CREDENTIALS]) {
                    sh "ssh -o StrictHostKeyChecking=no ahmed@${SWARM_SERVER} 'docker stack deploy --compose-file ~/DevOps-End-to-End/docker-compose.yml myapp --with-registry-auth'"
                }
            }
        }
    }

    post {
        failure {
            mail to: 'ahmed.dihe@gmail.com',
                 subject: 'Jenkins Build Failed: ${JOB_NAME} #${BUILD_NUMBER}',
                 body: 'Check Jenkins logs for details.'
        }
        success {
            mail to: 'ahmed.dihe@gmail.com',
                 subject: 'Jenkins Build Succeeded: ${JOB_NAME} #${BUILD_NUMBER}',
                 body: 'Check Jenkins logs for details.'
        }
    }
}


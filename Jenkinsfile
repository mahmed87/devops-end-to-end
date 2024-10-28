ipipeline {
    // ...

    stages {
        // ...

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDENTIALS,
                                                  usernameVariable: 'DOCKER_USER',
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker tag ahmeddihe/devops-end-to-end-backend:latest ahmeddihe/devops-end-to-end-backend:latest'
                    sh 'docker tag ahmeddihe/devops-end-to-end-frontend:latest ahmeddihe/devops-end-to-end-frontend:latest'
                    sh 'docker push ahmeddihe/devops-end-to-end-backend:latest'
                    sh 'docker push ahmeddihe/devops-end-to-end-frontend:latest'
                }
            }
        }

        // ...
    }

    post {
        failure {
            mail to: 'ahmed.dihe@gmail.com',
                 subject: 'Jenkins Build Failed: ${JOB_NAME} #${BUILD_NUMBER}',
                 body: 'Check Jenkins logs for details.',
                 from: 'ahmed.dihe@gmail.com',
                 smtpServer: 'ahmed.dihe@gmail.com'
        }
        success {
            mail to: 'ahmed.dihe@gmail.com',
                 subject: 'Jenkins Build Succeeded: ${JOB_NAME} #${BUILD_NUMBER}',
                 body: 'Check Jenkins logs for details.',
                 from: 'ahmed.dihe@gmail.com',
                 smtpServer: 'ahmed.dihe@gmail.com'
        }
    }
}

pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    cd /opt/apps/express-frontend
                    npm install
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    node --check server.js
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    cp server.js package.json package-lock.json /opt/apps/express-frontend/

                    cd /opt/apps/express-frontend

                    npm install

                    sudo -u ubuntu pm2 restart express-frontend
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                    sleep 3
                    curl -f http://localhost:3000
                '''
            }
        }
    }

    post {
        success {
            echo 'Express deployment successful!'
        }

        failure {
            echo 'Express deployment failed!'
        }
    }
}

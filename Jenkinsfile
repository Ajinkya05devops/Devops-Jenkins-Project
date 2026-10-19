pipeline {
    agent any

    // Jenkins checks GitHub every 2 minutes, so no webhook or ngrok is needed
    triggers {
        pollSCM('H/2 * * * *')
    }

    stages {
        stage('Checkout') {
            steps { checkout scm }
        }

        stage('Test') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate
                pip install -r requirements.txt
                pytest
                '''
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t myapp:${BUILD_NUMBER} .'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker rm -f myapp-live || true
                docker run -d --name myapp-live -p 5001:5000 myapp:${BUILD_NUMBER}
                '''
            }
        }
    }

    post {
        success { echo 'Pipeline succeeded, app is live on port 5001!' }
        failure { echo 'Pipeline failed, check the console output.' }
    }
}

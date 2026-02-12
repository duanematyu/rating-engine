pipeline {
    agent any

    environment {
        PYTHON_VERSION = '3.11'
        IMAGE_NAME = 'rating-engine'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/duanematyu/rating-engine.git'
            }
        }

        stage('Setup Python') {
            steps {
                bat 'python --version || sudo apt install python3 python3-pip -y'
                bat 'pip install --upgrade pip'
                bat 'pip install -r requirements.txt pytest flake8'
            }
        }

        stage('Lint') {
            steps {
                bat 'flake8 .'
            }
        }

        stage('Test') {
            steps {
                bat 'pytest'
            }
        }

        stage('Build Docker') {
            steps {
                bat 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Deploy') {
            steps {
                sshagent(credentials: ['SERVER_SSH_KEY_ID']) {
                    sh '''
                    scp rating-engine.tar user@yourserver:/home/user/
                    ssh user@yourserver "docker load < rating-engine.tar && docker stop rating-engine || true && docker rm rating-engine || true && docker run -d --name rating-engine rating-engine"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
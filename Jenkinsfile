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
                sh 'python --version || sudo apt install python3 python3-pip -y'
                sh 'pip install --upgrade pip'
                sh 'pip install -r requirements.txt pytest flake8'
            }
        }

        stage('Lint') {
            steps {
                sh 'flake8 .'
            }
        }

        stage('Test') {
            steps {
                sh 'pytest'
            }
        }

        stage('Build Docker') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
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
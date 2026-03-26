pipeline {
    agent any

    environment {
        IMAGE_NAME = "my-flask-app"
        IMAGE_TAG  = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Cloning repository...'
                checkout scm
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh '''
                    cd app
                    pip install -r requirements.txt --quiet
                    pytest test_app.py -v
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building image: ${IMAGE_NAME}:${IMAGE_TAG}"
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ./app"
                sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest"
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying container...'
                sh '''
                    docker stop my-flask-app || true
                    docker rm   my-flask-app || true
                    docker run -d \
                        --name my-flask-app \
                        -p 5000:5000 \
                        --restart unless-stopped \
                        my-flask-app:latest
                '''
            }
        }
    }

    post {
        success { echo '✅ Pipeline completed successfully!' }
        failure { echo '❌ Pipeline failed. Check the logs.' }
        always  { echo "Build #${BUILD_NUMBER} finished." }
    }
}
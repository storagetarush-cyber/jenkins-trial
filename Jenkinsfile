pipeline {
    agent any

    environment {
        IMAGE_NAME = "flask-demo-app"
        CONTAINER_NAME = "flask-live"
    }

    stages {
        stage('1. Checkout Code') {
            steps {
                echo 'Pulling latest code from GitHub...'
                checkout scm
            }
        }

        stage('2. Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .'
            }
        }

        stage('3. Run Automated Tests') {
            steps {
                echo 'Running pytest inside a temporary container...'
                sh 'docker run --rm ${IMAGE_NAME}:${BUILD_NUMBER} pytest'
            }
        }

        stage('4. Deploy Container') {
            steps {
                echo 'Replacing old container with the new build...'
                sh '''
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                    docker run -d --name ${CONTAINER_NAME} -p 5000:5000 --restart unless-stopped ${IMAGE_NAME}:${BUILD_NUMBER}
                '''
            }
        }
    }
}

pipeline {
    agent any

    environment {
        APP_NAME = "inventory-api"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Cloning the repository..."
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                bat "docker build -t %APP_NAME%:%IMAGE_TAG% ."
            }
        }

        stage('Test') {
            steps {
                echo "Running tests inside Docker container..."
                bat "docker run --rm %APP_NAME%:%IMAGE_TAG% npm test"
            }
        }

        stage('Deploy') {
            when {
                branch 'dev'
            }
            steps {
                echo "Deploying container for dev environment..."
                bat '''
                docker rm -f %APP_NAME%-dev || echo "No previous container"
                docker run -d --name %APP_NAME%-dev -p 3000:3000 %APP_NAME%:%IMAGE_TAG%
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Build, Test, and Deploy successful!"
        }
        failure {
            echo "❌ Pipeline failed. Check logs."
        }
    }
}

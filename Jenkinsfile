pipeline {
    agent any

    environment {
        // Updated credential ID and DockerHub username
        DOCKERHUB_CREDENTIALS = credentials('a770410e-e187-4471-ac23-fcff1d67da43')
        IMAGE_NAME = 'deepanshua04/webapp'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/deepanshuA04/CICD.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '''
                echo Building Docker image...
                docker build -t %IMAGE_NAME%:latest .
                '''
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                bat '''
                echo Logging in to DockerHub...
                echo %DOCKERHUB_CREDENTIALS_PSW% | docker login -u %DOCKERHUB_CREDENTIALS_USR% --password-stdin
                echo Pushing image to DockerHub...
                docker push %IMAGE_NAME%:latest
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat '''
                echo Deploying application to Kubernetes cluster...
                kubectl apply -f deployment.yaml --validate=false
                kubectl rollout status deployment/webapp-deployment
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully! Application deployed."
        }
        failure {
            echo "❌ Pipeline failed. Please check the console output for details."
        }
    }
}

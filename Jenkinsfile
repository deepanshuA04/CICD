pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-login')
        IMAGE_NAME = 'your-dockerhub-username/webapp'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/your-username/CICD2.git'
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
                kubectl apply -f deployment.yaml
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

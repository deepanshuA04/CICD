pipeline {
  agent any

  environment {
    IMAGE_NAME = "demo-app"
    IMAGE_TAG = "latest"
    DOCKER_CONTAINER_NAME = "demo-app-container"
    HOST_PORT = "8081"
    CONTAINER_PORT = "80" 
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build Docker Image') {
      steps {
        script {

          sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
        }
      }
    }

    stage('Stop & Remove Existing Container') {
      steps {
        script {

          sh """
            if [ \$(docker ps -a -q -f name=${DOCKER_CONTAINER_NAME} | wc -l) -gt 0 ]; then
              docker rm -f ${DOCKER_CONTAINER_NAME} || true
            fi
          """
        }
      }
    }

    stage('Run Container') {
      steps {
        script {

          sh """
            docker run -d --name ${DOCKER_CONTAINER_NAME} -p ${HOST_PORT}:${CONTAINER_PORT} ${IMAGE_NAME}:${IMAGE_TAG}
          """
        }
      }
    }

    stage('Smoke Test') {
      steps {
        script {

          sh """
            sleep 2
            echo '=== site content ==='
            curl -sS http://localhost:${HOST_PORT} || true
            echo '===================='
          """
        }
      }
    }
  }

  post {
    success {
      echo "Deployment finished. Open http://<JENKINS_HOST>:${HOST_PORT}"
    }
    failure {
      echo "Pipeline failed — check console output for errors."
    }
  }
}

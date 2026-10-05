pipeline {
  agent any

  environment {
    DOCKERHUB_USER = 'nourelhoude'
    IMAGE = "${DOCKERHUB_USER}/fleetman-webapp"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build image') {
      steps {
        sh 'docker build -t $IMAGE:$BUILD_NUMBER -t $IMAGE:latest .'
      }
    }

    stage('Push image') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'U', passwordVariable: 'P')]) {
          sh 'echo $P | docker login -u $U --password-stdin'
          sh 'docker push $IMAGE:$BUILD_NUMBER'
          sh 'docker push $IMAGE:latest'
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh 'kubectl set image deployment/webapp webapp=$IMAGE:$BUILD_NUMBER'
        sh 'kubectl rollout status deployment/webapp --timeout=120s'
      }
    }
  }

  post {
    success {
      echo "Pipeline réussi : image $IMAGE:$BUILD_NUMBER déployée."
    }
    failure {
      echo "Échec du pipeline."
    }
  }
}

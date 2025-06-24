pipeline {
  agent {
    kubernetes {
      yamlFile 'jenkins/k8s-agent.yaml'
    }
  }

  environment {
    DOCKER_IMAGE = '6065meet/portfolio-site'
    DOCKERHUB_CREDENTIALS = credentials('docker-credentials') // Add in Jenkins
  }

  stages {
    stage('Build Docker Image') {
      steps {
        container('docker') {
          sh 'docker build -t $DOCKER_IMAGE .'
        }
      }
    }

    stage('Push to DockerHub') {
      steps {
        container('docker') {
          withCredentials([usernamePassword(credentialsId: 'docker-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
            sh '''
              echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
              docker push $DOCKER_IMAGE
            '''
          }
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        echo 'You can apply kubectl deployment here if needed'
      }
    }
  }
}

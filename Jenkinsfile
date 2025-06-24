pipeline {
  agent {
    kubernetes {
      label 'devops-agent'
      defaultContainer 'kubectl'
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
    - name: kubectl
      image: bitnami/kubectl:latest
      command: ['cat']
      tty: true
      volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
  volumes:
    - name: docker-sock
      hostPath:
        path: /var/run/docker.sock
"""
    }
  }

  environment {
    DOCKER_IMAGE = "6065meet/devops-portfolio"
    NAMESPACE = "portfolio"
  }

  stages {
    stage('Checkout') {
      steps {
        container('kubectl') {
          git url: 'https://github.com/6065meet/devops-portfolio.git', branch: 'main'
        }
      }
    }

    stage('Build & Push Docker Image') {
      steps {
        container('kubectl') {
          withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
            sh '''
              echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
              docker build -t $DOCKER_IMAGE .
              docker push $DOCKER_IMAGE
            '''
          }
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        container('kubectl') {
          sh '''
            kubectl set image deployment/static-site-deployment static-site=$DOCKER_IMAGE -n $NAMESPACE
          '''
        }
      }
    }
  }
}

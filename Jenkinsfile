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
    - name: docker
      image: docker:24.0.2-dind
      securityContext:
        privileged: true
      volumeMounts:
        - name: docker-sock
          mountPath: /var/run/docker.sock

    - name: kubectl
      image: bitnami/kubectl:latest
      command: ['cat']
      tty: true

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
          withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
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
        container('kubectl') {
          sh '''
            kubectl set image deployment/static-site-deployment static-site=$DOCKER_IMAGE -n $NAMESPACE
          '''
        }
      }
    }
  }
}

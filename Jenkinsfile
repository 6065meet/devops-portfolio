pipeline {
    agent {
        kubernetes {
            yamlFile 'jenkins/k8s-agent.yaml'
            defaultContainer 'docker'
        }
    }
    environment {
        IMAGE_NAME = "6065meet/devops-portfolio"
        TAG = "latest"
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/6065meet/devops-portfolio'
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker build -t $IMAGE_NAME:$TAG .
                        docker push $IMAGE_NAME:$TAG
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f k8s/'
            }
        }
    }
}

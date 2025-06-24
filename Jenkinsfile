pipeline {
    agent {
        kubernetes {
            label 'devops-agent'
            yamlFile 'jenkins/k8s-agent.yaml'
        }
    }

    environment {
        DOCKER_USER = credentials('docker-username')
        DOCKER_PASS = credentials('docker-password')
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/6065meet/devops-portfolio.git'
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                container('docker') {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                        sh '''
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                            docker build -t 6065meet/devops-portfolio:latest .
                            docker push 6065meet/devops-portfolio:latest
                        '''
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                container('docker') {
                    sh '''
                        kubectl apply -f k8s/
                    '''
                }
            }
        }
    }
}

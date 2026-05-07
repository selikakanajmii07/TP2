pipeline {
    agent any

    environment {
        BACKEND_IMAGE = "selikakanajmii07/backend:latest"
        FRONTEND_IMAGE = "selikakanajmii07/frontend:latest"
    }

    stages {

        stage('Build Backend') {
            steps {
                sh 'docker build -t $BACKEND_IMAGE ./backend'
            }
        }

        stage('Build Frontend') {
            steps {
                sh 'docker build -t $FRONTEND_IMAGE ./frontend'
            }
        }

        stage('Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-cred',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {

                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin

                    docker push $BACKEND_IMAGE
                    docker push $FRONTEND_IMAGE
                    '''
                }
            }
        }

        stage('Deploy to AKS') {
            steps {

                sh '''
                kubectl apply -f backend-deployment.yaml
                kubectl apply -f backend-service.yaml
                kubectl apply -f frontend-deployment.yaml
                kubectl apply -f frontend-service.yaml
                kubectl apply -f ingress.yaml
                '''
            }
        }
    }
}

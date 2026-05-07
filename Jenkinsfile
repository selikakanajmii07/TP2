pipeline {
    agent any

    environment {
        BACKEND_IMAGE = "selikakanajmi/backend:latest"
        FRONTEND_IMAGE = "selikakanajmi/frontend:latest"
    }

    stages {

        stage('Build Backend') {
            steps {
                bat 'docker build -t %BACKEND_IMAGE% ./backend'
            }
        }

        stage('Build Frontend') {
            steps {
                bat 'docker build -t %FRONTEND_IMAGE% ./frontend'
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

                    bat '''
                    docker login -u %DOCKER_USER% -p %DOCKER_PASS%

                    docker push %BACKEND_IMAGE%
                    docker push %FRONTEND_IMAGE%
                    '''
                }
            }
        }

        stage('Deploy to AKS') {
            steps {

                bat '''
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

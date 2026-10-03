pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'anushka22hub/jenkins-docker-cicd'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Test') {
            steps {
                bat 'python -m pip install -r requirements.txt'
                bat 'python -m pytest'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE%:%BUILD_NUMBER% .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    bat 'docker login -u %DOCKER_USERNAME% -p %DOCKER_PASSWORD%'
                    bat 'docker push %DOCKER_IMAGE%:%BUILD_NUMBER%'
                    bat 'docker logout'
                }
            }
        }

        stage('Deploy') {
            steps {
                bat 'docker rm -f jenkins-cicd-app || exit 0'
                bat 'docker run -d -p 5000:5000 --name jenkins-cicd-app %DOCKER_IMAGE%:%BUILD_NUMBER%'
            }
        }
    }
}
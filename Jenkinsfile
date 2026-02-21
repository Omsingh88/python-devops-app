pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "omsingh07/python-devops-app"
    }

    stages {

        stage('Clone') {
            steps {
                git 'https://github.com/Omsingh88/python-devops-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t $DOCKER_IMAGE:${BUILD_NUMBER} ."
                    sh "docker tag $DOCKER_IMAGE:${BUILD_NUMBER} $DOCKER_IMAGE:latest"
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: '753c766b-84ad-4c17-a753-5cf476c07316',
                    usernameVariable: 'USERNAME',
                    passwordVariable: 'PASSWORD'
                )]) {
                    sh """
                        echo $PASSWORD | docker login -u $USERNAME --password-stdin
                        docker push $DOCKER_IMAGE:${BUILD_NUMBER}
                        docker push $DOCKER_IMAGE:latest
                    """
                }
            }
        }
    }
}

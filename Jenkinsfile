pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "omsingh07/python-devops-app"
        CONTAINER_NAME = "python-app"
        PORT = "5000"
        APP_SERVER = "ubuntu@98.93.18.133"   // 👈 Replace this
    }

    stages {

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

        stage('Deploy to App Server') {
            steps {
                sshagent(['app-server-ssh']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no $APP_SERVER '
                        docker pull $DOCKER_IMAGE:${BUILD_NUMBER} &&
                        docker stop $CONTAINER_NAME || true &&
                        docker rm $CONTAINER_NAME || true &&
                        docker run -d -p $PORT:$PORT --name $CONTAINER_NAME $DOCKER_IMAGE:${BUILD_NUMBER}
                        '
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful!"
        }
        failure {
            echo "❌ Pipeline Failed!"
        }
    }
}

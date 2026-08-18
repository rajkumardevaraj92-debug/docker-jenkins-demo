pipeline {

    agent any

    environment {
        DOCKER_IMAGE = "rajkumard92/docker-jenkins-demo"
        DOCKER_CREDENTIALS = "dockerhub-credentials"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'

                sh '''
                    docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} .
                    docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_IMAGE}:latest
                '''
            }
        }

        stage('Docker Login') {
            steps {
                echo 'Logging into Docker Hub...'

                withCredentials([
                    usernamePassword(
                        credentialsId: "${DOCKER_CREDENTIALS}",
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {

                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                        -u "$DOCKER_USERNAME" \
                        --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {

                sh '''
                    docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}
                    docker push ${DOCKER_IMAGE}:latest
                '''
            }
        }

        stage('Run Docker Container') {
            steps {

                echo 'Deploying Docker container...'

                sh '''
                    docker pull ${DOCKER_IMAGE}:latest

                    docker stop docker-jenkins-demo || true
                    docker rm docker-jenkins-demo || true

                    docker run -d \
                        --name docker-jenkins-demo \
                        -p 8081:80 \
                        ${DOCKER_IMAGE}:latest

                    docker ps
                '''
            }
        }
    }

    post {

        success {
            echo 'Docker image successfully built, pushed and deployed!'
        }

        failure {
            echo 'Pipeline failed!'
        }

        always {
            sh 'docker logout || true'
        }
    }
}

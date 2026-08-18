stage('Run Docker Container') {
    steps {
        echo 'Starting Docker container...'

        sh '''
            docker pull ${DOCKER_IMAGE}:latest

            docker stop docker-jenkins-demo || true
            docker rm docker-jenkins-demo || true

            docker run -d \
                --name docker-jenkins-demo \
                -p 8080:80 \
                ${DOCKER_IMAGE}:latest
        '''
    }
}

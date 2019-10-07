pipeline {
    agent any
    environment {
        DOCKER_CONTAINER_NAME="mcox-api"
        DOCKER_IMAGE_NAME="mcox-api"
        DOCKER_REGISTRY="registry.docker.io"
        ENV_FILE_LOCATION="/home/calvin.low/mcox/mcox-api.env"
    }
    stages {
        stage('Checkout'){
            steps {
                checkout scm
            }
        }
        stage('Build') {
            environment{ 
                PATH = "/usr/local/bin:/var/run/docker.sock:$PATH"
            } 
            steps {
                script {
                    def image = docker.build("${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${env.BUILD_ID}")
                    image.push()
                }
            }
        }
        stage('Deploy') {
            steps {
                sshagent (credentials: ['deploy-dev']) {
                    sh 'ssh -o StrictHostKeyChecking=no -l calvin.low.kw 172.17.36.28 docker rm -f ${DOCKER_CONTAINER_NAME}'
                    sh 'ssh -o StrictHostKeyChecking=no -l calvin.low.kw 172.17.36.28 docker run --name ${DOCKER_CONTAINER_NAME} --env-file ${ENV_FILE_LOCATION} -d ${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${env.BUILD_ID}'
                }
            }
            
        }
        stage('Complete') {
            steps {
                echo "Complete deployment for ${DOCKER_CONTAINER_NAME} with image ${DOCKER_IMAGE_NAME}:${env.BUILD_ID}."
            }
        }
    }
}
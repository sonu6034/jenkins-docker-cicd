pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "sonu6034/jenkins-docker-cicd:latest"
        DOCKER_CREDENTIALS_ID = "docker-hub-creds"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('', DOCKER_CREDENTIALS_ID) {
                        echo 'Logged in'
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', DOCKER_CREDENTIALS_ID) {
                        sh 'docker push $DOCKER_IMAGE'
                    }
                }
            }
        }

        stage('Run Container') {
            steps {
                script {
                    sh '''
                    docker rm -f myapp || true
                    docker run -d --name myapp -p 5555:5000 $DOCKER_IMAGE
                    '''
                }
            }
        }
    }
}


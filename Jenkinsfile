pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = "docker-hub-creds"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Get Commit Hash') {
            steps {
                script {
                    COMMIT_HASH = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
                    IMAGE_TAG = "sonu6034/jenkins-docker-cicd:${COMMIT_HASH}"
                    env.IMAGE_TAG = IMAGE_TAG
                    echo "Docker image will be tagged as: ${IMAGE_TAG}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                        docker pull ${env.IMAGE_TAG} || true
                        docker build --cache-from=${env.IMAGE_TAG} -t ${env.IMAGE_TAG} .
                    """
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('', env.DOCKER_CREDENTIALS_ID) {
                        echo 'Logged in to Docker Hub'
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', env.DOCKER_CREDENTIALS_ID) {
                        sh "docker push ${env.IMAGE_TAG}"
                    }
                }
            }
        }

        stage('Run Container') {
            steps {
                script {
                    sh """
                        docker rm -f myapp || true
                        docker run -d --name myapp -p 5555:5000 ${env.IMAGE_TAG}
                    """
                }
            }
        }
    }
}

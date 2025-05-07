pipeline {
    agent any

    environment {
        GIT_REPOSITORY_URL = 'https://github.com/sandesh300/docker_jenkins_demo.git'
        DOCKER_IMAGE_NAME = 'sandesh030/docker_jenkins_demo'
        IMAGE_TAG = '1.0'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: GIT_REPOSITORY_URL
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    try {
                        // Correct: Build using Dockerfile in the current dir
                        dockerImage = docker.build("${DOCKER_IMAGE_NAME}:${IMAGE_TAG}", ".")
                    } catch (Exception e) {
                        error "Failed to build Docker image: ${e.message}"
                    }
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'my-docker-hub-credentials-id',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )]) {
                        sh """
                            echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                            docker push ${DOCKER_IMAGE_NAME}:${IMAGE_TAG}
                        """
                    }
                }
            }
        }
    }
}

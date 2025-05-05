pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'hello-world-app'
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/por314159ug/app-python.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build(env.DOCKER_IMAGE)
                }
            }
        }
        stage('Clean Up Old Container') {
            steps {
                script {
                    def containerId = bat(
                        script: "FOR /F \"tokens=*\" %%i IN ('docker ps -q --filter \"ancestor=%DOCKER_IMAGE%\" --filter \"publish=5000\"') DO @echo %%i",
                        returnStdout: true
                    ).trim()
        
                    if (containerId) {
                        echo "Stopping and removing container: ${containerId}"
                        bat "docker stop ${containerId}"
                        bat "docker rm ${containerId}"
                    } else {
                        echo "No container found using image ${env.DOCKER_IMAGE} and port 5000"
                    }
                }
            }
        }
        
        stage('Run Docker Container') {
            steps {
                script {
                    docker.image("${env.DOCKER_IMAGE}:latest").run("-p 5000:5000")
                }
            }
        }
    }
}

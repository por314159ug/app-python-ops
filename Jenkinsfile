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
                        script: """
                        @echo off
                        for /f "tokens=1" %%i in ('docker ps --filter "publish=5000" --format "{{.ID}}"') do (
                            docker stop %%i
                            docker rm %%i
                            echo %%i
                        )
                        """,
                        returnStdout: true
                    ).trim()
        
                    if (containerId) {
                        echo "Stopped and removed container: ${containerId}"
                    } else {
                        echo "No container found on port 5000"
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

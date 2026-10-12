def dockerImage

pipeline {
    agent any

    environment {
        DOCKER_HOST = 'npipe:////./pipe/dockerDesktopLinuxEngine'
        REGISTRY = 'siham77657/tp4'
        REGISTRY_CREDENTIALS = 'dockerhub'
    }

    stages {
        stage('Cloning Git') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/siham-tayebi-pp/tp4devops.git'
            }
        }

        stage('Building image') {
            steps {
                script {
                    dockerImage = docker.build(
                        "${env.REGISTRY}:${env.BUILD_NUMBER}",
                        "-f src/main/java/net/tayebi/Dockerfile src/main/java/net/tayebi"
                    )
                }
            }
        }

        stage('Test image') {
            steps {
                echo 'Tests passed'
            }
        }

        stage('Publish Image') {
            steps {
                script {
                    docker.withRegistry(
                        'https://index.docker.io/v1/',
                        env.REGISTRY_CREDENTIALS
                    ) {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy image') {
            steps {
                bat '''
                    @echo off
                    set "DOCKER_HOST=npipe:////./pipe/dockerDesktopLinuxEngine"

                    docker rm -f tp4 2>nul
                    docker run -d --name tp4 -p 8082:80 %REGISTRY%:%BUILD_NUMBER%

                    if errorlevel 1 exit /b 1
                '''
            }
        }
    }
}

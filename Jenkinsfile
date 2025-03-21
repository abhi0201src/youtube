pipeline {
    agent any

    // Define parameters
    parameters {
        choice(name: 'BRANCH', choices: ['main', 'development', 'feature'], description: 'Select the branch to build')
    }

    environment {
        CI = 'false' // Treat warnings as warnings, not errors
        repourl = 'https://github.com/abhi0201src/youtube.git'
        DOCKER_IMAGE = 'abhi0201/youtube' // Base Docker image name
    }

    stages {
        stage('Clone') {
            steps {
                echo 'Cloning repo'
                git branch: "${params.BRANCH}", url: "${repourl}"
            }
        }

        stage('Verify') {
            steps {
                echo 'Verifying'
                sh 'whoami'
            }
        }

        stage('Login to Docker') {
            steps {
                echo 'Logging in to Docker'
                sh 'docker login'
            }
        }

        stage('Build Dockerfile') {
            steps {
                echo 'Building Dockerfile'
                script {
                    // Append build number to the Docker image tag
                    def imageTag = "${env.DOCKER_IMAGE}:1.0.${env.BUILD_NUMBER}"
                    sh "docker build -t ${imageTag} ."
                    // Store the image tag for later stages
                    env.DOCKER_IMAGE_TAG = imageTag
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing image to Docker Hub'
                sh "docker push ${env.DOCKER_IMAGE_TAG}"
            }
        }

        stage('Stop and Remove Existing Container') {
            steps {
                echo 'Stopping and removing existing container on port 3000'
                script {
                    // Check if a container is running on port 3000
                    def runningContainerId = sh(script: "docker ps -q --filter 'publish=3000'", returnStdout: true).trim()
                    if (runningContainerId) {
                        echo "Stopping and removing container: ${runningContainerId}"
                        sh "docker stop ${runningContainerId}"
                        sh "docker rm ${runningContainerId}"
                    } else {
                        echo "No container is running on port 3000"
                    }
                }
            }
        }

        stage('Run Docker Image') {
            steps {
                echo 'Running Docker image'
                sh "docker run -d -p 3000:3000 ${env.DOCKER_IMAGE_TAG}"
            }
        }
    }
}

pipeline {
    agent any

    parameters {
        choice(name: 'BRANCH', choices: ['main', 'development', 'feature'], description: 'Select the branch to build')
    }

    environment {
        CI = 'false'
        repourl = 'https://github.com/abhi0201src/youtube.git'
        DOCKER_IMAGE = 'abhi0201/youtube'
        EC2_HOST = 'ec2-3-83-47-202.compute-1.amazonaws.com'
        EC2_USER = 'jenkins-agent'
    }

    stages {
        stage('Clone') {
            steps {
                echo 'Cloning repo'
                git branch: "${params.BRANCH}", url: "${repourl}"
            }
        }

        stage('Login to Docker') {
            steps {
                echo 'Logging in to Docker'
                withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin"
                }
            }
        }

        stage('Build Dockerfile') {
            steps {
                echo 'Building Dockerfile'
                script {
                    def imageTag = "${env.DOCKER_IMAGE}:1.0.${env.BUILD_NUMBER}"
                    sh "docker build -t ${imageTag} ."
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

        stage('Deploy to EC2') {
            steps {
                sshagent(['aws']) {
                    script {
                        // SSH into EC2 and execute Docker commands
                        def remoteCommands = """
                            echo 'Pulling Docker image from Docker Hub'
                            docker pull ${env.DOCKER_IMAGE_TAG}
                            
                            echo 'Stopping and removing existing container on port 3000'
                            docker ps -q --filter publish=3000 | xargs -r docker stop
                            docker ps -aq --filter publish=3000 | xargs -r docker rm
                            
                            echo 'Running new Docker container'
                            docker run -d -p 3000:3000 ${env.DOCKER_IMAGE_TAG}
                        """
                        
                        sh "ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} '${remoteCommands}'"
                    }
                }
            }
        }
    }
}
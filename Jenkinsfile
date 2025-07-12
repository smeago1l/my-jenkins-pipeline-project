pipeline {
    agent any

    tools {
        nodejs 'node-7.8.0'
    }

    environment {
        DOCKER_IMAGE_NAME = ''
        DOCKER_CONTAINER_NAME = ''
        DEPLOY_PORT = ''
    }

    stages {
        stage('Initialize') {
            steps {
                script {
		    echo "--- Initializing for branch: ${env.BRANCH_NAME} ---"
                    if (env.BRANCH_NAME == 'main') {
                        env.DOCKER_IMAGE_NAME = 'nodemain:v1.0'
                        env.DOCKER_CONTAINER_NAME = 'app-main'
                        env.DEPLOY_PORT = 3000
                    } else if (env.BRANCH_NAME == 'dev') {
                        env.DOCKER_IMAGE_NAME = 'nodedev:v1.0'
                        env.DOCKER_CONTAINER_NAME = 'app-dev'
                        env.DEPLOY_PORT = 3001
                    } else {
                        // This will stop the build if the branch is not main or dev
                        error("This pipeline is only configured for 'main' and 'dev' branches. Branch name was: ${env.BRANCH_NAME}")
                    }
                    echo "Docker Image to be built: ${env.DOCKER_IMAGE_NAME}"
                    echo "Container name for deployment: ${env.DOCKER_CONTAINER_NAME}"
                    echo "Port to be exposed: ${env.DEPLOY_PORT}"
                    echo "------------------------------------------------"
                }
            }
        }

        stage('Build Application') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test Application') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${env.DOCKER_IMAGE_NAME} ."
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh "docker stop ${env.DOCKER_CONTAINER_NAME} || true"
                    sh "docker rm ${env.DOCKER_CONTAINER_NAME} || true"
                    sh "docker run -d --name ${env.DOCKER_CONTAINER_NAME} -p ${env.DEPLOY_PORT}:80 ${env.DOCKER_IMAGE_NAME}"
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
}

pipeline {
    agent any

    tools {
        nodejs 'NodeJS_7.8.0'
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
                    if (env.BRANCH_NAME == 'main') {
                        env.DOCKER_IMAGE_NAME = 'nodemain:v1.0'
                        env.DOCKER_CONTAINER_NAME = 'app-main'
                        env.DEPLOY_PORT = 3000
                    } else if (env.BRANCH_NAME == 'dev') {
                        env.DOCKER_IMAGE_NAME = 'nodedev:v1.0'
                        env.DOCKER_CONTAINER_NAME = 'app-dev'
                        env.DEPLOY_PORT = 3001
                    } else {
                        error("This pipeline is only configured for 'main' and 'dev' branches.")
                    }
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

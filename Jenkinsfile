pipeline {
    agent {label 'linux-agent' }
    tools {
        nodejs 'NodeJS 16.8.0'
    }
    environment {
        DOCKER_HUB_CREDENTIALS = credentials("hub.docker")
        DOCKER_IMAGE_NAME = "rest-api/metal-slug-maker"
    }
    stages {
        stage('install packages') {
            steps {
                sh "npm install"
            }
        }
        stage('run unit tests') {
            steps {
                sh "npm test"
            }
        }
        stage('run lint validation') {
            steps {
                sh "npm run lint"
            }
        }
        stage('build Image') {
            steps {
                sh "docker build -t $DOCKER_IMAGE_NAME:$BUILD_NUMBER ."
            }
        }
        stage('push Image') {
            steps {
                sh "echo '$DOCKER_HUB_CREDENTIALS_PSW' | docker login -u $DOCKER_HUB_CREDENTIALS_USR --password-stdin"
            }
            post {
                always {
                    script {
                        sh "docker rmi -f $DOCKER_IMAGE_NAME:$BUILD_NUMBER"
                        sh "docker logout"
                    }
                }
            }
        }
    }
}
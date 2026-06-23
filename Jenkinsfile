pipeline {
    agent any

    options {
        disableConcurrentBuilds()
    }

    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'Rama del repo a desplegar')
    }

    triggers {
        pollSCM('H/2 * * * *')
    }

    environment {
        IMAGE_NAME = 'react-demo'
        CONTAINER_NAME = 'react-demo-app'
        APP_PORT = '8082'
        GIT_URL = 'git@github.com:tonysantacruz/react-demo.git'
        GIT_CREDENTIALS = 'github-ssh-react-v2'
    }

    stages {
        stage('Checkout branch') {
            steps {
                checkout([$class: 'GitSCM',
                    branches: [[name: "*/${params.BRANCH_NAME}"]],
                    userRemoteConfigs: [[url: env.GIT_URL, credentialsId: env.GIT_CREDENTIALS]]
                ])
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'node:20-alpine'
                    reuseNode true
                }
            }
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} -t ${IMAGE_NAME}:latest ."
            }
        }

        stage('Deploy') {
            steps {
                sh """
                    docker rm -f ${CONTAINER_NAME} || true
                    docker run -d --name ${CONTAINER_NAME} -p ${APP_PORT}:80 --restart unless-stopped ${IMAGE_NAME}:latest
                """
            }
        }
    }
}

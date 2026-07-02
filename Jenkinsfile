pipeline {
    agent any

    environment {
        IMAGE_NAME = 'myapp'
        CONTAINER_NAME = 'myapp-prod'
    }

    stages {
        stage('Checkout') {
            steps { checkout scm }
        }

        stage('Build Image') {
            steps {
                script {
                    app = docker.build("${IMAGE_NAME}:${env.BUILD_ID}")
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    app.inside { sh 'npm test' }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh "docker stop ${CONTAINER_NAME} || true"
                    sh "docker rm ${CONTAINER_NAME} || true"
                    app.run("-d --name ${CONTAINER_NAME} -p 8081:3000")
                }
            }
        }
    }

    post {
        always {
            sh "docker image prune -f"
        }
    }
}
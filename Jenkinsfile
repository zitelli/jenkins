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
                    app.inside {
                        sh 'npm test'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Para o container antigo (se existir) via plugin
                    try {
                        docker.container(CONTAINER_NAME).stop()
                        docker.container(CONTAINER_NAME).remove(force: true)
                    } catch (e) {
                        // ignorar se não existir
                    }
                    // Inicia o novo container
                    app.run("-d --name ${CONTAINER_NAME} -p 8081:3000")
                }
            }
        }
    }

    post {
        always {
            script {
                // Limpa imagens dangling via plugin (sem CLI)
                docker.imagePrune()
            }    
        }
    }
}
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'myapp'
        CONTAINER_NAME = 'myapp-prod'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
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
                    // Run tests inside the built image
                    app.inside {
                        sh 'npm test'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Stop and remove the old container
                    sh "docker stop ${CONTAINER_NAME} || true"
                    sh "docker rm ${CONTAINER_NAME} || true"
                    // Start the new container, mapping host port 80 to container port 3000
                    app.run("-d --name ${CONTAINER_NAME} -p 8081:3000")
                }
            }
        }
    }

    post {
        always {
            // Clean up old dangling images (plugin‑based)
            docker.imagePrune(filters: 'dangling=true')
        }
    }
}

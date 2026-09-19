pipeline {
    agent any

    environment {
        APP_NAME = 'jenkins-docker-demo'
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${APP_NAME}:${IMAGE_TAG}"
        CONTAINER_NAME = "${APP_NAME}-container"
        HOST_PORT = '8081'
        CONTAINER_PORT = '80'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image: ${IMAGE_NAME}"
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Docker Image Test') {
            steps {
                echo 'Testing Docker image...'
                sh """
                    docker run -d \
                        --name ${CONTAINER_NAME}-test \
                        -p 18081:${CONTAINER_PORT} \
                        ${IMAGE_NAME}

                    sleep 5

                    curl -f http://localhost:18081

                    docker stop ${CONTAINER_NAME}-test
                    docker rm ${CONTAINER_NAME}-test
                """
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying Docker container...'

                sh """
                    docker stop ${CONTAINER_NAME} 2>/dev/null || true
                    docker rm ${CONTAINER_NAME} 2>/dev/null || true

                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        -p ${HOST_PORT}:${CONTAINER_PORT} \
                        --restart unless-stopped \
                        ${IMAGE_NAME}

                    sleep 5

                    docker ps --filter "name=${CONTAINER_NAME}" \
                        --format "table {{.Names}}\\t{{.Status}}\\t{{.Ports}}"
                """
            }
        }

        stage('Deployment Verification') {
            steps {
                echo 'Verifying deployed application...'
                sh """
                    curl -f http://localhost:${HOST_PORT}

                    echo ""
                    echo "======================================"
                    echo "BUILD SUCCESSFUL"
                    echo "DEPLOYMENT SUCCESSFUL"
                    echo "Application: ${APP_NAME}"
                    echo "Image: ${IMAGE_NAME}"
                    echo "URL: http://localhost:${HOST_PORT}"
                    echo "======================================"
                """
            }
        }
    }

    post {
    success {
        emailext(
            subject: "SUCCESS: ${JOB_NAME} #${BUILD_NUMBER}",
            body: """
Build SUCCESSFUL

Job: ${JOB_NAME}
Build: #${BUILD_NUMBER}
URL: ${BUILD_URL}

The Jenkins Docker deployment completed successfully.
""",
            to: 'your-email@example.com'
        )
    }

    failure {
        emailext(
            subject: "FAILED: ${JOB_NAME} #${BUILD_NUMBER}",
            body: """
Build FAILED

Job: ${JOB_NAME}
Build: #${BUILD_NUMBER}
URL: ${BUILD_URL}

Please check the Jenkins console output for the failure details.
""",
            to: 'your-email@example.com'
        )
    }
}

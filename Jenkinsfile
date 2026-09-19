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

                sh """
                    docker build -t ${IMAGE_NAME} .
                """
            }
        }

        stage('Docker Image Test') {
            steps {
                echo 'Testing Docker image...'

                sh """
                    docker run -d \
                        --name ${CONTAINER_NAME}-test \
                        -p 18080:${CONTAINER_PORT} \
                        ${IMAGE_NAME}

                    sleep 5

                    echo "Testing application..."

                    curl -f http://localhost:18080

                    echo ""
                    echo "Docker image test successful!"

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

                    echo "Checking deployed container..."

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
                    echo "======================================"
                    echo "Application : ${APP_NAME}"
                    echo "Image       : ${IMAGE_NAME}"
                    echo "Container   : ${CONTAINER_NAME}"
                    echo "URL         : http://localhost:${HOST_PORT}"
                    echo "======================================"
                """
            }
        }
    }

    post {

        success {
            echo 'Jenkins pipeline completed successfully!'

            emailext(
                to: 'vasanthk.official@outlook.com',
                subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Hello,

Jenkins pipeline completed successfully.

========================================
JENKINS BUILD DETAILS
========================================

Job Name       : ${env.JOB_NAME}
Build Number   : ${env.BUILD_NUMBER}
Build Status   : SUCCESS

Application    : ${APP_NAME}
Docker Image   : ${IMAGE_NAME}
Container      : ${CONTAINER_NAME}

Application URL:
http://localhost:${HOST_PORT}

Jenkins Build URL:
${env.BUILD_URL}

========================================
BUILD AND DEPLOYMENT SUCCESSFUL
========================================

Regards,
Jenkins
""",
                attachLog: true,
                compressLog: true
            )
        }

        failure {
            echo 'Jenkins pipeline failed.'

            emailext(
                to: 'vasanthk.official@outlook.com',
                subject: "FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Hello,

Jenkins pipeline has FAILED.

========================================
JENKINS BUILD DETAILS
========================================

Job Name       : ${env.JOB_NAME}
Build Number   : ${env.BUILD_NUMBER}
Build Status   : FAILURE

Application    : ${APP_NAME}
Docker Image   : ${IMAGE_NAME}
Container      : ${CONTAINER_NAME}

Jenkins Build URL:
${env.BUILD_URL}

Please check the attached Jenkins console log
to identify the failure.

========================================
BUILD / DEPLOYMENT FAILED
========================================

Regards,
Jenkins
""",
                attachLog: true,
                compressLog: true
            )

            sh """
                docker rm -f ${CONTAINER_NAME} 2>/dev/null || true
                docker rm -f ${CONTAINER_NAME}-test 2>/dev/null || true
            """
        }

        always {
            echo 'Cleaning unused Docker images...'

            sh """
                docker image prune -f || true
            """
        }
    }
}

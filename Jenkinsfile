pipeline {
    agent any

    parameters {
        string(
            name: 'ENV',
            defaultValue: 'dev',
            description: 'Enter environment: dev or prod'
        )
    }

    stages {

        stage('Validate') {
            steps {
                sh '''
                    echo "Selected environment: $ENV"

                    if [ "$ENV" != "dev" ] && [ "$ENV" != "prod" ]; then
                        echo "Invalid environment"
                        exit 1
                    fi

                    echo "Environment is valid"
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    if [ "$ENV" = "dev" ]; then
                        echo "Deploying to DEVELOPMENT environment"
                        echo "Running development deployment..."

                    elif [ "$ENV" = "prod" ]; then
                        echo "Deploying to PRODUCTION environment"
                        echo "Running production deployment..."
                    fi
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully"
        }

        failure {
            echo "Pipeline failed"
        }
    }
}

pipeline {
    agent any 

    paramenters {
        string(
           name: 'ENV',
            defaultname: 'dev'
            description: 'Enter the environment: dev or prod'
        )
    }

    stages {

        stage('validate') {
            steps {
                sh '''
                echo "Selected enviroment: $ENV"

                if ["$ENV" != "dev"] && ["$ENV" != "prod"]; then
                echo "invalid environment"
                exit 1
                fi

                echo "environment is valid"
                '''
            }
    }

        stage('deploy'){
            steps {
                sh '''
                if ["$ENV" = "dev"] then
                echo "deploying to development environment"
                echo "Running development deploying..."

                elif ["$ENV" = "prod"] then
                echo "Deploying to production environment"
                echo "Running production deploying..."
                fi
                '''
            }
        }
    }

        post {
            success{
                echo "pipeline completed successfully"
            }

            failure{
                echo "pipeline failed"
            }
        }
             
    }

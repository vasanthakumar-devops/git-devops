pieline {
    agent any 
    parameters{
        string(
            Name: 'ENV',
            DefaultValue: 'Dev',
            Description: 'Enter the environment: dev or prod'
            )
    }
    stage {
        stage ('Validate') {
            step{
                sh '''
                echo "Select environment: $ENV"

                if ["$ENV" != "dev"] && ["$ENV" != "prod"]; then
                echo "Invalid environment"
                exit 1
                fi
                echo "Environment is valid"
                '''
                }
                }    
                
                stage ('Deploy') {
                step {
                sh '''
                if ["$ENV" = "dev"]; then
                echo "Deploying to Development environment"
                echo "Running development deployment"

                elif ["$ENV" = "prod"]; then 
                echo "Deploying to production environment"
                echo "Running production environment"
                fi
                '''
                }
                }
    }

    post {
        success {
            echo "pipeline compeleted successfully"
        }

        failure {
            echo "Pipeline failed"
        }
    }
}

                    

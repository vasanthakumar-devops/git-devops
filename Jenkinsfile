pipeline {
    agent any

    parameters {
        string(
            name: 'NAME',
            defaultValue: 'Jenkins',
            description: 'Enter your name'
        )
    }

    stages {
        stage('Build') {
            steps {
                echo "Hello ${params.NAME}"

                sh """
                    echo "Running build for ${params.NAME}"
                """
            }
        }
    }
}

pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {

        stage('Source Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Configuration Validation') {
            steps {
                sh '''
                    test -f Dockerfile
                    test -f index.html
                    echo "Configuration validation successful"
                '''
            }
        }

        stage('Application Tests') {
            steps {
                sh '''
                    grep -q "Hello Jenkins Integration" index.html
                    echo "Application test passed"
                '''
            }
        }

        stage('Docker Image Build') {
            steps {
                sh '''
                    docker build -t jenkins-demo:${BUILD_NUMBER} .
                '''
            }
        }

        stage('Image Tagging') {
            steps {
                sh '''
                    docker tag jenkins-demo:${BUILD_NUMBER} jenkins-demo:latest
                    docker images jenkins-demo
                '''
            }
        }
    }

    post {
        success {
            echo 'CI Pipeline completed successfully!'
        }

        failure {
            echo 'CI Pipeline failed. Check console output.'
        }

        always {
            echo 'Pipeline execution finished.'
        }
    }
}


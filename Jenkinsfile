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

        stage('Push to Amazon ECR') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'aws-ecr-credentials',
                        usernameVariable: 'AWS_ACCESS_KEY_ID',
                        passwordVariable: 'AWS_SECRET_ACCESS_KEY'
                    )
                ]) {
                    sh '''
                        export AWS_DEFAULT_REGION=ap-south-1

                        AWS_ACCOUNT_ID=997785426159
                        ECR_REPOSITORY=jenkins-demo
                        ECR_REGISTRY=${AWS_ACCOUNT_ID}.dkr.ecr.ap-south-1.amazonaws.com

                        echo "Logging into Amazon ECR..."

                        aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin $ECR_REGISTRY

                        echo "Tagging Docker images..."

                        docker tag jenkins-demo:${BUILD_NUMBER} $ECR_REGISTRY/$ECR_REPOSITORY:${BUILD_NUMBER}

                        docker tag jenkins-demo:latest $ECR_REGISTRY/$ECR_REPOSITORY:latest

                        echo "Pushing images to Amazon ECR..."

                        docker push $ECR_REGISTRY/$ECR_REPOSITORY:${BUILD_NUMBER}

                        docker push $ECR_REGISTRY/$ECR_REPOSITORY:latest

                        echo "ECR push completed successfully!"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check console output.'
        }

        always {
            echo 'Pipeline execution finished.'
        }
    }
}

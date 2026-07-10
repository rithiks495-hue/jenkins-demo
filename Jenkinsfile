pipeline {

    agent any

    stages {

        stage('Credentials Demo') {

            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'github-creds',
                        usernameVariable: 'USERNAME',
                        passwordVariable: 'PASSWORD'
                    )
                ]) {

                    sh '''
                    echo Username is $USERNAME
                    echo Password Loaded Successfully
                    '''

                }

            }

        }

    }

}
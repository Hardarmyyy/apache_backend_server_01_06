pipeline {

    agent { label "agent-node-1" }

    stages {

        stage('Checkout') {

            steps {
            
                git branch: 'main', url: 'https://github.com/Hardarmyyy/apache_backend_server_01_06.git'

                echo 'Source code from github repository'
            }

        }

        stage('Install dependencies') {

            steps {
            
                sh 'npm install'

                echo 'Installing dependencies'

                archiveArtifacts artifacts: '**/*', excludes: 'temp/**, *.log', allowEmptyArchive: true
            }

        }

        stage('Build docker image') {

            steps {

                // Build Docker image with build-time envirnment arguments
                script {
                    // Load the .env file
                    def envFile = managedFile id: 'env-file-id', targetLocation: '.env'
                    sh "docker-compose --env-file .env build"

                }
            }

        }

        stage('Deploy docker image') {

            steps {
                // Deploy image to dockerhub registery
                withCredentials([usernamePassword(credentialsId: "${DOCKER_REGISTRY_CREDS}", passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {

                    sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin docker.io"

                    sh "docker-compose push"

                }
            }

        }

    }

    post {

        always {
            //clean workspace after every build
            cleanWs()

            sh 'docker logout'
        }

    }

}

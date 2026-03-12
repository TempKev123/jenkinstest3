pipeline { 

    agent any

    environment {

        // Replace with your actual Docker Hub username

        DOCKER_HUB_USER = 'tempkev123'

        IMAGE_NAME = 'todo-app-jenkins'

        // This ID must match the Credential ID created in Jenkins (Username with Password)

        DOCKER_HUB_CREDS = 'docker-hub-credentials-test' 

    } 
    
    stages {

        stage('Build') {
            steps {
                echo 'Building the application...'

                // Install dependencies
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests...'

                // If tests fail here, the pipeline will stop automatically
                sh 'npm test'
            }
        }

        stage('Containerize') {
            steps {
                echo 'Creating Docker image...'

                // Build Docker image
                sh "docker build -t ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest ."
            }
        }

        stage('Push') {
            steps {
                echo 'Logging into Docker Hub and pushing image...'

                // Use Jenkins credentials instead of hardcoding password
                withCredentials([
                    usernamePassword(
                        credentialsId: "${DOCKER_HUB_CREDS}",
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {

                    sh "echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin"
                    sh "docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest"

                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace...'

            // Remove image to save disk space
            sh "docker rmi ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest || true"
        }
    }
}
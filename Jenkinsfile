pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                // Build the Docker image
                script {
                    dockerImage = docker.build("my-node-app:${env.BUILD_ID}")
                }
            }
        }
        
        stage('Test') {
            steps {
                // Run tests inside the Docker container (assuming you have a "test" script in your package.json)
                script {
                    dockerImage.inside {
                        sh 'npm install'
                        sh 'npm test'
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                // Push the Docker image to the Docker registry (optional)
                script {
                    withDockerRegistry([credentialsId: 'YOUR_DOCKER_CREDENTIALS_ID', url: 'https://index.docker.io/v1/']) {
                        dockerImage.push()
                    }
                }
                // Add deployment steps here if needed
            }
        }
    }

    post {
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}

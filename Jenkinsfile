pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
        IMAGE_NAME = 'hanisntsolo/react-fe'
        SERVER_SSH_CREDENTIALS = credentials('cloud-ssh-id')
        SERVER_USER = 'root'
        SERVER_IP = '34.152.7.19'
        SERVER_DESTINATION_FOLDER = '/docker/lab/deployed'
	    SSH_KEY=''
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }
        stage('Build Docker Image') {
                steps {
                    // Build the Docker image using the Dockerfile in your project directory
                    sh "docker build -t $IMAGE_NAME ."
                }
            }
        stage('Publish to Docker Hub') {
            steps {
                script {
                    // Authenticate with Docker Hub using the provided credentials
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        // Push the Docker image to Docker Hub
                        sh "docker push $IMAGE_NAME"
                    }
                }
            }
        }
        stage('Deploy to Server') {
            steps {
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: 'cloud-ssh-id', keyFileVariable: 'SSH_KEY')]) {
                        sh "ssh -i $SSH_KEY $SERVER_USER@$SERVER_IP 'docker stop react-fe || true && docker rm react-fe || true'"
                        sh "ssh -i $SSH_KEY $SERVER_USER@$SERVER_IP 'docker run -d -p 3000:3000 --name react-fe $IMAGE_NAME'"
                    }
                }
            }
        }
    }
}

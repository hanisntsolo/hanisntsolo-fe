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
                    withCredentials([string(credentialsId: 'DOCKER_HUB_CREDENTIALS', variable: 'DOCKER_HUB_CREDENTIALS')]) {
                        sh 'docker login -u your-docker-hub-username -p $DOCKER_HUB_CREDENTIALS'
                        sh 'docker push $IMAGE_NAME'
                }
            }
        }
        stage('Deploy to Server') {
            steps {
                    // Your deployment steps here, e.g., using SSH or any other method to deploy the Docker image on your server
                script {
                        //SSH into the server and deploy
                    withCredentials([sshUserPrivateKey(credentialsId: 'SERVER_SSH_CREDENTIALS', keyFileVariable: 'SSH_KEY')]) {
                    sh "ssh -i $SSH_KEY $SERVER_USER@$SERVER_IP 'docker stop react-fe || true && docker rm react-fe || true'"
                    sh "ssh -i $SSH_KEY $SERVER_USER@$SERVER_IP 'docker run -d -p 3000:3000 --name react-fe $IMAGE_NAME'"
                    }
                }
            }
        }
    }
}

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
                    // Fetch the SSH key and store it in a variable
                    withCredentials([sshUserPrivateKey(credentialsId: 'cloud-ssh-id', keyFileVariable: 'SSH_KEY')]) {
                        // Fetch and store the host key fingerprint
                        def remoteHost = '34.152.7.19'
                        def remoteHostKey = sh(returnStdout: true, script: "ssh-keyscan ${remoteHost}")
                        // Customize the location of the known hosts file
                        def knownHostsFile = '/var/jenkins_home/.ssh/known_hosts'
                        
                        // Compare the stored fingerprint with the one from the server
                        def fingerprintCommand = "ssh-keygen -lf -"
                        def currentFingerprint = sh(returnStdout: true, script: "${fingerprintCommand}", input: remoteHostKey).trim()
                        def expectedFingerprint = 'YOUR_EXPECTED_FINGERPRINT' // Replace this with the expected fingerprint
                        
                        if (currentFingerprint == expectedFingerprint) {
                            echo "Host key verification successful!"
                            // Your deployment steps here, e.g., using SSH or any other method to deploy the Docker image on your server
                            sh "ssh -i $SSH_KEY $SERVER_USER@$SERVER_IP 'docker stop react-fe || true && docker rm react-fe || true'"
                            sh "ssh -i $SSH_KEY $SERVER_USER@$SERVER_IP 'docker run -d -p 3000:3000 --name react-fe $IMAGE_NAME'"
                        } else {
                            error "Host key verification failed! Possible man-in-the-middle attack."
                        }
                    }
                }
            }
        }
    }
}

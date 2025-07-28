pipeline {
    agent any

    environment {
        IMAGE_NAME = "bodkekarbalaji95/flask-devops-app"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/bodkekarbalaji95/my-devops-demo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $IMAGE_NAME .'
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push $IMAGE_NAME
                    '''
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['my_key_ssh']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ec2-ubuntu@EC2_3.88.210.132 "
                        docker pull $IMAGE_NAME &&
                        docker stop flask-devops || true &&
                        docker rm flask-devops || true &&
                        docker run -d --name flask-devops -p 5000:5000 $IMAGE_NAME
                    "
                    '''
                }
            }
        }
    }
}

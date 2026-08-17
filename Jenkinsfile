pipeline {
    agent any

    environment {
        DOCKERHUB_CREDS = credentials('dockerhub-creds')
        SERVER_IMAGE = "moonknightmaf/pixelboard-server"
        CLIENT_IMAGE = "moonknightmaf/pixelboard-client"
    }

    stages {
        stage('Build Server Image') {
            steps {
                sh 'docker build -t ${SERVER_IMAGE}:${BUILD_NUMBER} -t ${SERVER_IMAGE}:latest ./Server'
            }
        }
        stage('Build Client Image') {
            steps {
                sh 'docker build --build-arg VITE_SOCKET_URL=http://localhost:3001 -t ${CLIENT_IMAGE}:${BUILD_NUMBER} -t ${CLIENT_IMAGE}:latest ./Client'
            }
        }
        stage('Push Images') {
            steps {
                sh 'echo $DOCKERHUB_CREDS_PSW | docker login -u $DOCKERHUB_CREDS_USR --password-stdin'
                sh 'docker push ${SERVER_IMAGE}:${BUILD_NUMBER}'
                sh 'docker push ${SERVER_IMAGE}:latest'
                sh 'docker push ${CLIENT_IMAGE}:${BUILD_NUMBER}'
                sh 'docker push ${CLIENT_IMAGE}:latest'
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker compose down || true'
                sh 'docker compose up -d --build'
            }
        }
    }
}
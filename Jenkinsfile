pipeline {
    agent {
        label 'windows'
    }

    environment {
        DOCKERHUB_CREDS = credentials('dockerhub-creds')
        SERVER_IMAGE = "moonknightmaf/pixelboard-server"
        CLIENT_IMAGE = "moonknightmaf/pixelboard-client"
    }

    stages {
        stage('Build Server Image') {
            steps {
                bat 'docker build -t %SERVER_IMAGE%:%BUILD_NUMBER% -t %SERVER_IMAGE%:latest ./Server'
            }
        }

        stage('Build Client Image') {
            steps {
                bat 'docker build --build-arg VITE_SOCKET_URL=http://localhost:3001 -t %CLIENT_IMAGE%:%BUILD_NUMBER% -t %CLIENT_IMAGE%:latest ./Client'
            }
        }

        stage('Push Images') {
            steps {
                bat 'echo %DOCKERHUB_CREDS_PSW% | docker login -u %DOCKERHUB_CREDS_USR% --password-stdin'
                bat 'docker push %SERVER_IMAGE%:%BUILD_NUMBER%'
                bat 'docker push %SERVER_IMAGE%:latest'
                bat 'docker push %CLIENT_IMAGE%:%BUILD_NUMBER%'
                bat 'docker push %CLIENT_IMAGE%:latest'
            }
        }

        stage('Deploy') {
            steps {
                bat 'docker compose down || exit /b 0'
                bat 'docker compose pull'
                bat 'docker compose up -d'
            }
        }
    }
}

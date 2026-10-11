
pipeline {
    agent any

   
environment {
    IMAGE_NAME = 'book-library-system'
    NGINX_PORT = '8084'
    DOCKER = 'C:\\Users\\Arceo\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
    COMPOSE = 'C:\\Users\\Arceo\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker-compose.exe'
}


    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Check Tools') {
            steps {
                bat 'node --version'
                bat 'npm --version'
                bat '"%DOCKER%" --version'
                bat '"%COMPOSE%" version'
            }
        }

        stage('Run Tests') {
            steps {
                bat '"%COMPOSE%" down --remove-orphans'
                bat '"%COMPOSE%" up -d database'
                bat '"%COMPOSE%" run --rm --build --user root web sh -c "npm install --include=dev && npm test"'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '"%DOCKER%" build -t %IMAGE_NAME%:%BUILD_NUMBER% .'
                bat '"%DOCKER%" tag %IMAGE_NAME%:%BUILD_NUMBER% %IMAGE_NAME%:latest'
            }
        }

        stage('Deploy') {
            steps {
                bat '"%COMPOSE%" up -d --build'
            }
        }

        stage('Smoke Test') {
            steps {
                bat '"%COMPOSE%" ps'
                bat 'curl.exe --fail http://localhost:8083/health'
            }
        }
    }

    post {
        always {
            bat '"%COMPOSE%" ps'
        }
    }
}

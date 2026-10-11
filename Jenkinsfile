
pipeline {
    agent any

    environment {
    IMAGE_NAME = 'book-library-system'
    DOCKER = 'C:\\Users\\Arceo\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
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
                bat 'docker compose version'
            }
        }

        stage('Run Tests') {
            steps {
                bat 'docker compose down --remove-orphans'
                bat 'docker compose up -d database'
                bat 'docker compose run --rm --build --user root web sh -c "npm install --include=dev && npm test"'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME%:%BUILD_NUMBER% .'
                bat 'docker tag %IMAGE_NAME%:%BUILD_NUMBER% %IMAGE_NAME%:latest'
            }
        }

        stage('Deploy') {
            steps {
                bat 'docker compose up -d --build'
            }
        }

        stage('Smoke Test') {
            steps {
                bat 'docker compose ps'
                bat 'curl.exe --fail http://localhost:8082/health'
            }
        }
    }

    post {
        always {
            bat 'docker compose ps'
        }
    }
}

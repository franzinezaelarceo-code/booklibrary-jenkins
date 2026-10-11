
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
                bat '"%DOCKER%" compose version'
            }
        }

        stage('Run Tests') {
            steps {
                bat '"%DOCKER%" compose down --remove-orphans'
                bat '"%DOCKER%" compose up -d database'
                bat '"%DOCKER%" compose run --rm --build --user root web sh -c "npm install --include=dev && npm test"'
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
                bat '"%DOCKER%" compose up -d --build'
            }
        }

        stage('Smoke Test') {
            steps {
                bat '"%DOCKER%" compose ps'
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

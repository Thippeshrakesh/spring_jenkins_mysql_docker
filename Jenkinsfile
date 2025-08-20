pipeline {
    agent any
    tools { 
        maven 'maven' 
    }

    environment {
        COMPOSE_FILE = 'docker-compose.yml'
    }

    stages {
        stage('Build JAR') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Start Services with Docker Compose') {
            steps {
                sh "docker-compose -f ${COMPOSE_FILE} up -d --build"
            }
        }

        stage('Show Running Containers') {
            steps {
                sh "docker ps --filter name=spring-app-container"
                sh "docker ps --filter name=mysql-container"
            }
        }
    }

    post {
        success {
            echo "✅ Spring Boot app and MySQL started successfully with Docker Compose."
        }
        failure {
            echo "❌ Deployment failed. Check logs above."
        }
        always {
            echo "ℹ️ Pipeline finished."
        }
    }
}

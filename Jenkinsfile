pipeline {
    agent any
    tools { 
        maven 'maven' 
    }

    environment {
        COMPOSE_FILE = 'docker-compose.yml'
    }

    stages {
        stage('Install Docker & Compose') {
            steps {
                script {
                    echo "⚡ Checking & Installing Docker and Docker Compose if not present..."

                    // Install Docker if missing
                    sh '''
                    if ! command -v docker &> /dev/null
                    then
                      echo "🐳 Installing Docker..."
                      sudo apt-get update -y
                      sudo apt-get install -y ca-certificates curl gnupg lsb-release
                      sudo mkdir -p /etc/apt/keyrings
                      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
                      echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
                      sudo apt-get update -y
                      sudo apt-get install -y docker-ce docker-ce-cli containerd.io
                      echo "✅ Docker installed successfully."
                    else
                      echo "✅ Docker already installed."
                    fi
                    '''

                    // Install Docker Compose if missing
                    sh '''
                    if ! command -v docker-compose &> /dev/null
                    then
                      echo "📦 Installing Docker Compose..."
                      sudo apt-get update -y
                      sudo apt-get install -y docker-compose-plugin
                      echo "✅ Docker Compose installed successfully."
                    else
                      echo "✅ Docker Compose already installed."
                    fi
                    '''
                }
            }
        }

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

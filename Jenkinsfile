pipeline {
    agent any
    
    environment {
        SONAR_TOKEN = credentials('sonarqube-token')
        SONAR_HOST_URL = 'http://172.191.74.240:9000'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Verify Tools') {
            steps {
                sh 'node --version'
                sh 'npm --version'
                sh 'sonar-scanner --version'
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                sh '''
                    sonar-scanner \
                      -Dsonar.projectKey=teclado-project \
                      -Dsonar.sources=. \
                      -Dsonar.host.url=${SONAR_HOST_URL} \
                      -Dsonar.token=${SONAR_TOKEN}
                '''
            }
        }
        
        stage('File Validation') {
            steps {
                sh 'ls -la'
                sh 'find . -name "*.js" -o -name "*.json" | head -10'
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline ejecutado exitosamente!'
        }
        failure {
            echo 'Pipeline falló. Revisar logs.'
        }
    }
}
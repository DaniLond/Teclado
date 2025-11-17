pipeline {
    agent any
    
    environment {
        // URL de SonarQube (se toma de la configuración global de Jenkins)
        SONAR_HOST_URL = "${env.SONARQUBE_URL}"
        
        // Configuración del proyecto
        SONAR_PROJECT_KEY = "teclado-project"
        SONAR_PROJECT_NAME = "Teclado - Aplicación de Práctica de Mecanografía"
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo ' Clonando repositorio...'
                checkout scm
            }
        }
        
        stage('Verificar Herramientas') {
            steps {
                echo ' Verificando herramientas instaladas...'
                sh '''
                    echo "Node.js version:"
                    node --version
                    echo "NPM version:"
                    npm --version
                    echo "SonarScanner version:"
                    sonar-scanner --version
                '''
            }
        }
        
        stage('Análisis de Código con SonarQube') {
            steps {
                echo ' Iniciando análisis estático de código...'
                withCredentials([string(credentialsId: 'sonarqube-token', variable: 'SONAR_TOKEN')]) {
                    sh """
                        sonar-scanner \
                          -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                          -Dsonar.projectName='${SONAR_PROJECT_NAME}' \
                          -Dsonar.sources=. \
                          -Dsonar.exclusions=css/**,**/*.css \
                          -Dsonar.javascript.file.suffixes=.js \
                          -Dsonar.host.url=${SONAR_HOST_URL} \
                          -Dsonar.token=${SONAR_TOKEN}
                    """
                }
            }
        }
        
        stage('Validación de Archivos') {
            steps {
                echo ' Validando estructura del proyecto...'
                sh '''
                    echo "Archivos en el workspace:"
                    ls -la
                    
                    echo "\nValidando archivos críticos..."
                    if [ ! -f "index.html" ]; then
                        echo " ERROR: index.html no encontrado"
                        exit 1
                    fi
                    
                    if [ ! -f "script.js" ]; then
                        echo " ERROR: script.js no encontrado"
                        exit 1
                    fi
                    
                    if [ ! -d "css" ]; then
                        echo " ERROR: directorio css/ no encontrado"
                        exit 1
                    fi
                    
                    echo " Todos los archivos críticos están presentes"
                '''
            }
        }
    }
    
    post {
        success {
            echo ' Pipeline ejecutado exitosamente!'
            echo " Revisa los resultados en SonarQube: ${SONAR_HOST_URL}/dashboard?id=${SONAR_PROJECT_KEY}"
        }
        failure {
            echo ' El pipeline falló. Revisa los logs para más detalles.'
        }
        always {
            echo ' Pipeline finalizado.'
        }
    }
}
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo ' Código descargado desde GitHub'
            }
        }
        
        stage('Verificar Herramientas') {
            steps {
                sh 'node --version'
                sh 'npm --version'
                echo ' Node.js y npm verificados'
            }
        }
        
        stage('Análisis de Código Estático') {
            steps {
                echo ' Análisis básico de archivos JavaScript...'
                sh '''
                    echo "Archivos .js encontrados:"
                    find . -name "*.js" -not -path "*/node_modules/*" | wc -l
                    
                    echo ""
                    echo "Primeros 10 archivos .js:"
                    find . -name "*.js" -not -path "*/node_modules/*" | head -10
                '''
                echo ' Análisis completado'
            }
        }
        
        stage('Validación de package.json') {
            steps {
                script {
                    if (fileExists('package.json')) {
                        echo ' package.json encontrado'
                        sh 'cat package.json | head -20'
                    } else {
                        echo '  package.json no encontrado'
                    }
                }
            }
        }
        
        stage('Reporte de Proyecto') {
            steps {
                sh '''
                    echo "================================"
                    echo "  RESUMEN DEL PROYECTO"
                    echo "================================"
                    echo "Directorio: $(pwd)"
                    echo "Archivos totales: $(find . -type f | wc -l)"
                    echo "Archivos .js: $(find . -name "*.js" -not -path "*/node_modules/*" | wc -l)"
                    echo "Archivos .json: $(find . -name "*.json" | wc -l)"
                    echo "================================"
                '''
            }
        }
    }
    
    post {
        success {
            echo ' Pipeline ejecutado EXITOSAMENTE!'
            echo ' Proyecto Teclado validado correctamente'
        }
        failure {
            echo ' Pipeline falló. Revisar logs arriba.'
        }
    }
}
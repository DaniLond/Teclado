pipeline {
    agent any
    
    environment {
        NGINX_SERVER = '52.170.95.59'
        NGINX_USER = 'adminuser'
        DEPLOY_PATH = '/var/www/html/teclado'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Verificar Herramientas') {
            steps {
                sh '''
                    echo "Verificando Node.js..."
                    node --version
                    echo "Verificando npm..."
                    npm --version
                '''
            }
        }
        
        stage('Análisis de Código Estático') {
            steps {
                sh '''
                    echo "Analizando archivos JavaScript..."
                    find . -name "*.js" -not -path "*/node_modules/*" | head -20
                    echo "Total de archivos .js:"
                    find . -name "*.js" -not -path "*/node_modules/*" | wc -l
                '''
            }
        }
        
        stage('Validación de package.json') {
            steps {
                script {
                    if (fileExists('package.json')) {
                        echo "✅ package.json encontrado"
                        sh 'cat package.json'
                    } else {
                        echo "ℹ️ No hay package.json (proyecto HTML simple)"
                    }
                }
            }
        }
        
        stage('Reporte de Proyecto') {
            steps {
                sh '''
                    echo "=== REPORTE DEL PROYECTO ==="
                    echo "Archivos totales:"
                    find . -type f | wc -l
                    echo ""
                    echo "Archivos JavaScript:"
                    find . -name "*.js" | wc -l
                    echo ""
                    echo "Archivos JSON:"
                    find . -name "*.json" | wc -l
                    echo ""
                    echo "Directorio actual:"
                    pwd
                    echo ""
                    echo "Contenido del directorio:"
                    ls -lah
                '''
            }
        }
        
        stage('Desplegar a Nginx') {
            steps {
                script {
                    echo "🚀 Desplegando aplicación a servidor Nginx..."
                    
                    // Crear directorio si no existe
                    sh """
                        ssh -i /var/jenkins_home/.ssh/id_rsa -o StrictHostKeyChecking=no ${NGINX_USER}@${NGINX_SERVER} '
                            sudo mkdir -p ${DEPLOY_PATH}
                            sudo chown ${NGINX_USER}:${NGINX_USER} ${DEPLOY_PATH}
                        '
                    """
                    
                    // Copiar archivos
                    sh """
                        rsync -avz --delete \
                            -e "ssh -i /var/jenkins_home/.ssh/id_rsa -o StrictHostKeyChecking=no" \
                            --exclude='.git' \
                            --exclude='Jenkinsfile' \
                            --exclude='.gitignore' \
                            ./ ${NGINX_USER}@${NGINX_SERVER}:${DEPLOY_PATH}/
                    """
                    
                    echo "✅ Despliegue completado"
                    echo "📱 Aplicación disponible en: http://${NGINX_SERVER}/teclado"
                }
            }
        }
    }
    
    post {
        success {
            echo '✅ Pipeline ejecutado exitosamente - Aplicación desplegada'
        }
        failure {
            echo '❌ Pipeline falló - Verificar logs'
        }
    }
}

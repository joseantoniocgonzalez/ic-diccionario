pipeline {
    agent {
        docker {
            image 'debian:latest'
            args '-u root:root'  // Ejecuta como root
        }
    }
    environment {
        LC_ALL = 'C.UTF-8'  // Configuración de UTF-8
        DEBIAN_FRONTEND = 'noninteractive'  // Evita prompts durante la instalación
    }
    stages {

        stage('Clone') {
            steps {
                echo 'Clonando repositorio...'
                git url: 'https://github.com/joseantoniocgonzalez/ic-diccionario', branch: 'master'
            }
        }

        stage('Install') {
            steps {
                echo 'Actualizando e instalando aspell-es...'
                sh '''
                    apt-get update
                    apt-get install -y aspell-es
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Realizando comprobación ortográfica...'
                sh '''
                    # Verificación de archivos
                    if [ -f doc/index.md ] && [ -f doc/index2.md ]; then
                        cat doc/index.md doc/index2.md | aspell list -d es -p ./.aspell.es.pws
                    else
                        echo "⚠️ Archivos doc/index.md o doc/index2.md no encontrados."
                    fi
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline finalizado correctamente.'
            mail to: 'usuario@gonzalonazareno.org',
                 subject: "✅ Éxito: ${currentBuild.fullDisplayName}",
                 body: "🔗 Detalles: ${env.BUILD_URL}\nResultado: ${currentBuild.result}"
        }
        failure {
            echo '❌ Ocurrió un error en el pipeline.'
            mail to: 'usuario@gonzalonazareno.org',
                 subject: "❌ Fallo: ${currentBuild.fullDisplayName}",
                 body: "🔗 Detalles: ${env.BUILD_URL}\nResultado: ${currentBuild.result}"
        }
        always {
            echo '📧 Se ha enviado una notificación por correo.'
        }
    }
}

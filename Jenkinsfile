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
        always {
            script {
                def changes = ""
                if (currentBuild.changeSets.size() > 0) {
                    currentBuild.changeSets.each { changeSet ->
                        changeSet.items.each { item ->
                            changes += "- ${item.commitId.take(7)}: ${item.msg} (por ${item.author})\n"
                        }
                    }
                } else {
                    changes = "No hubo cambios desde el último build."
                }

                mail to: 'er.joselin@gmail.com',
                     subject: "🔔 Estado del pipeline: ${currentBuild.fullDisplayName}",
                     body: """🔗 Detalles del build: ${env.BUILD_URL}
Resultado: ${currentBuild.result}

📝 Cambios desde el último build:
${changes}
"""
            }
        }
    }
}

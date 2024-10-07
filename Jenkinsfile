pipeline {
    agent {
        docker {
            image 'node:20.13.1'  // Imagen de Docker que se usará
            args '-u root'  // Permite instalar dependencias en fases posteriores
        }
    }

    stages {
        stage('Install') {
            steps {
                echo "Installing dependencies..."
                sh 'npm ci'  // Instala dependencias desde package-lock.json
                stash includes: 'node_modules/**', name: 'node_modules'  // Guarda node_modules como un stash
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                unstash 'node_modules'  // Recupera node_modules del stash
                sh 'npm run test'  // Ejecuta el script de pruebas
                sh 'npm run coverage'  // Ejecuta el script de cobertura
                junit 'junit.xml'  // Publica el reporte en formato JUnit
                archiveArtifacts artifacts: 'coverage/**, junit.xml', allowEmptyArchive: true  // Guarda cobertura y reporte como artefactos
            }
        }

        stage('Build') {
            steps {
                echo "Building the project..."
                unstash 'node_modules'  // Recupera node_modules del stash
                sh 'npm run build'  // Ejecuta la construcción del proyecto
                stash includes: 'dist/**', name: 'dist'  // Guarda el directorio de distribución para futuras referencias
            }
        }
    }

    post {
        always {
            cleanWs()  // Limpia el espacio de trabajo después de la ejecución
        }
    }
}

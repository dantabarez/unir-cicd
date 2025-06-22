pipeline {
    agent any // Ejecuta el pipeline en cualquier agente disponible

    stages {
        stage('Source') { // Descarga el código fuente del repositorio
            steps {
                git 'https://github.com/dantabarez/unir-cicd.git'
            }
        }
        stage('Build') { // Construye las imágenes Docker necesarias
            steps {
                echo 'Building stage!'
                sh 'make build'
            }
        }
        stage('Unit tests') { // Ejecuta pruebas unitarias y archiva el resultado
            steps {
                sh 'make test-unit'
                archiveArtifacts artifacts: 'results/unit_result.xml'
            }
        }
        stage('API tests') { // Ejecuta pruebas de API y archiva el resultado
            steps {
                sh 'make test-api'
                archiveArtifacts artifacts: 'results/api_result.xml'
            }
        }
        stage('E2E tests') { // Ejecuta pruebas end-to-end (E2E) y archiva el resultado
            steps {
                sh 'make test-e2e'
                archiveArtifacts artifacts: 'results/cypress_result.xml'
            }
        }
    }
    post {
        always {
            // Publica todos los reportes de pruebas y limpia el workspace
            junit 'results/*_result.xml,results/cypress_result.xml'
            cleanWs()
        }
        failure {
            // Envía un correo si el pipeline falla, con información del trabajo y número de ejecución
            mail to: 'dan.tabarez@gmail.com',
                 subject: "Fallo en el pipeline: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "El pipeline '${env.JOB_NAME}' con el número de ejecución ${env.BUILD_NUMBER} ha fallado. Revisa los logs y reportes adjuntos en Jenkins."
        }
    }
}
pipeline {
    agent any

    environment {
        NETWORK_NAME = 'redcalendario'
    }

    triggers {
        githubPush()
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Crear Red Docker') {
            steps {
                script {
                    def existe = sh(
                        script: "docker network ls --filter name=${NETWORK_NAME} --format '{{.Name}}' | grep -w ${NETWORK_NAME}",
                        returnStatus: true
                    )
                    if (existe != 0) {
                        sh "docker network create ${NETWORK_NAME}"
                        echo "Red '${NETWORK_NAME}' creada."
                    } else {
                        echo "Red '${NETWORK_NAME}' ya existe."
                    }
                }
            }
        }

        stage('Detener Contenedores Anteriores') {
            steps {
                sh 'docker compose down --remove-orphans'
            }
        }

        stage('Construir Imagen Docker') {
            steps {
                sh 'docker compose build --no-cache'
            }
        }

        stage('Desplegar Contenedores') {
            steps {
                sh 'docker compose up -d'
            }
        }

        stage('Verificar Estado') {
            steps {
                sh 'docker compose ps'
            }
        }

    }

    post {
        success {
            echo 'Despliegue de apiFestivos (Express + MongoDB) completado exitosamente.'
        }
        failure {
            echo 'Error en el despliegue de apiFestivos. Revisando logs...'
            sh 'docker compose logs --tail=50'
        }
    }
}

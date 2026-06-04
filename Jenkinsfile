pipeline {
    agent {
        kubernetes {
            yamlFile 'agent.yaml'
        }
    }
    environment {
        DOCKER_CREDS = credentials('docker-hub-credentials-id')
        IMAGE_NAME = "bvergara7/tarea-final:benjamin-vergara"
    }
    stages {
        stage('Install') {
            steps {
                container('node') {
                    echo 'Instalando dependencias...'
                    sh 'npm install -g pnpm'
                    sh 'pnpm install'
                }
            }
        }
        stage('Test') {
            steps {
                container('node') {
                    echo 'Ejecutando pruebas...'
                    sh 'pnpm run test'
                }
            }
        }
        stage('Build') {
            steps {
                container('docker') {
                    sh "docker build -t ${IMAGE_NAME} ."
                }
            }
        }
        stage('Push') {
            steps {
                container('docker') {
                    sh "echo \$DOCKER_CREDS_PSW | docker login -u \$DOCKER_CREDS_USR --password-stdin"
                    sh "docker push ${IMAGE_NAME}"
                }
            }
        }
        stage('Deploy') {
            steps {
                container('kubectl') {
                    sh "kubectl apply -f entrega.yaml"
                    sh "kubectl rollout status deployment/app-benjamin-vergara -n ns-benjamin-vergara"
                }
            }
        }
    }
}
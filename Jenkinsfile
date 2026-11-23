pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Validar Helm Chart') {
            steps {
                sh 'helm lint k8s/helm/glpi'
                sh 'helm lint k8s/helm/nginx'
            }
        }
    }
}

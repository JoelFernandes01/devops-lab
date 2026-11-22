pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Helm Lint & Validation') {
            steps {
                sh 'helm lint helm/glpi'
                sh 'helm template helm/glpi --debug'
            }
        }
        
        stage('GitOps Trigger') {
            steps {
                echo 'Validacao concluida! O ArgoCD sincronizara o cluster automaticamente via GitOps.'
            }
        }
    }
}

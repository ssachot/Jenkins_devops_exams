pipeline {
    agent any

    environment {
        KUBECONFIG = credentials('KUBECONFIG') // Kubeconfig dans Jenkins
        GIT_REPO = 'https://github.com/ssachot/Jenkins_devops_exams.git' // dépôt Git
        CHART_PATH = 'helm-chart/jenkinsexam'
    }

    stages {
        stage('Deploy to Dev') {
            steps {
                script {
                    deployToKubernetes('dev')
                }
            }
        }

        stage('Deploy to QA') {
            steps {
                script {
                    deployToKubernetes('qa')
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                script {
                    deployToKubernetes('staging')
                }
            }
        }

        stage('Deploy to Prod') {
            when {
                branch 'master'
            }
            steps {
                script {
                    input message: 'Deploy to Production?', ok: 'Deploy'
                    deployToKubernetes('prod')
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}

def deployToKubernetes(env) {
    // Déployer avec Helm sur Kubernetes directement depuis le dépôt GitHub
    sh """
        export KUBECONFIG=\$KUBECONFIG
        helm repo add myrepo \$GIT_REPO
        helm repo update
        helm upgrade --install ${env}-deployment myrepo/$CHART_PATH --namespace ${env} --set environment=${env}
    """
}


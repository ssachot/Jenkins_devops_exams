
pipeline {
    agent any

    environment {
        KUBECONFIG = credentials('kubeconfig-credentials-id') // kubeconfig-credentials-id à Remplacer par l'ID du credential dans Jenkins
        GIT_REPO = 'https://github.com/ssachot/Jenkins_devops_exams.git' // Remplacez par l'URL du dépôt Git
        CHART_PATH = 'helm-chart/jenkinsexam'
    }

    stages {
        stage('Clone Repository') {
            steps {
                // Cloner le dépôt Git dans l'espace de travail de Jenkins
                git url: "${GIT_REPO}", branch: 'main'
            }
        }

        stage('Dev') {
            steps {
                script {
                    deployToKubernetes('dev')
                }
            }
            post {
                always {
                    uninstallFromKubernetes('dev')
                }
            }
        }

        stage('QA') {
            steps {
                script {
                    deployToKubernetes('qa')
                }
            }
            post {
                always {
                    uninstallFromKubernetes('qa')
                }
            }
        }

        stage('Staging') {
            steps {
                script {
                    deployToKubernetes('staging')
                }
            }
            post {
                always {
                    uninstallFromKubernetes('staging')
                }
            }
        }

        stage('Prod') {
            when {
                branch 'master'
            }
            input{
				message "Press Ok to deploy in prod"
				}
			
			steps {
                script {
                    input message: 'Deploy to Production?', ok: 'Deploy'
                    deployToKubernetes('prod')
                }
            }
            post {
                always {
                    script {
                        input message: 'Uninstall from Production?', ok: 'Uninstall'
                        uninstallFromKubernetes('prod')
                    }
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
    // Déployer avec Helm sur Kubernetes en utilisant le chart local cloné
    sh """
        export KUBECONFIG=\$KUBECONFIG
        helm upgrade --install ${env}-deployment ${CHART_PATH} --namespace ${env} --set environment=${env}
    """
}

def uninstallFromKubernetes(env) {
    // Désinstaller avec Helm sur Kubernetes
    sh """
        export KUBECONFIG=\$KUBECONFIG
        helm uninstall ${env}-deployment --namespace ${env}
    """
}

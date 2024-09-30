pipeline {
    agent {
        label 'agent-master'
    }

    stages{
        stage("Git Checkout"){
            steps{
                sh "git branch: 'build_deploy_k8s', credentialsId: 'github-credentials', url: 'https://github.com/ajaycentos/gke_jenkins_argocd.git'"
            }
        }
    }
}


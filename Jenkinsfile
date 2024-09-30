pipeline {
    agent {
        label 'agent-nodes'
    }

    stages{
        stage("Git Checkout"){
            steps{
                sh "git branch: 'branch4', credentialsId: 'github-credentials', url: 'https://github.com/ajaycentos/gke_jenkins_argocd.git'"
            }
        }
    }
}


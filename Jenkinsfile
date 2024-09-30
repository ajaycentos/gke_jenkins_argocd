currentBuild.displayName="aj-app-#"+currentBuild.number

pipeline {
    agent {
        label 'agent-master'
    }

    stages{
        stage("Git Checkout"){
            steps{
                git branch: 'build_deploy_k8s', credentialsId: 'github-credentials', url: 'https://github.com/ajaycentos/gke_jenkins_argocd.git'
            }
        }
    }
}


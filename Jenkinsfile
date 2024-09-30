currentBuild.displayName="aj-app-#"+currentBuild.number

pipeline {
    agent {
        label 'agent-master'
    }
    environment{
        DOCKER_TAG = getDockerTag()
    }

    stages{
        stage("Git Checkout"){
            steps{
                git branch: 'build_deploy_k8s', credentialsId: 'github-credentials', url: 'https://github.com/ajaycentos/gke_jenkins_argocd.git'
            }
        }
        stage('Build Docker image'){
            steps{
                sh "docker build . -t ajaycentos/sampleapp:${DOCKER_TAG}"
            }
        }
        stage('Dockerhub Push'){  
            steps{
                    withCredentials([string(credentialsId: 'docker-pwd-only', variable: 'dockerhub-pwd')]) {
                       sh "docker login -u ajaycentos -p ${dockerhub-pwd}"
                       sh "docker push ajaycentos/sampleapp:${DOCKER_TAG}"
            }          

            }
        }
    }
}

def getDockerTag(){
    def tag = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag 
}


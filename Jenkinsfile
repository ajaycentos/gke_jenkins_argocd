//This will change build display in Jobs
currentBuild.displayName="aj-app-#"+currentBuild.number

//Declaratiev pipeline
pipeline {

    agent {
        label 'agent-master'
    }

    // If declared as environment this will be global
    environment{
        DOCKER_TAG = getDockerTag()
    }

    stages{
        stage("Git Checkout"){
            steps{
                git branch: 'build_deploy_k8s_argocd', credentialsId: 'github-credentials', url: 'https://github.com/ajaycentos/gke_jenkins_argocd.git'
            }
        }
        stage('Build Docker image'){
            steps{
                sh "cat main.js"
                sh "docker build . -t ajaycentos/sampleapp:${DOCKER_TAG}"
            }
        }
        stage('Dockerhub Push'){  
            steps{
                    withCredentials([string(credentialsId: 'docker-pwd-only', variable: 'dockerhubpwd')]) {
                       sh "docker login -u ajaycentos -p ${dockerhubpwd}"
                       sh "docker push ajaycentos/sampleapp:${DOCKER_TAG}"
            }          

            }
        }

        stage('Update Deployment Files in Repo') {
            steps {
                // Clone the deployment repo
                dir('deployment_repo') {
                    git branch: 'main', credentialsId: 'git_user_token', url: 'https://github.com/ajaycentos/gke_jenkins_argocd_deployment.git'
                    
                    
                    // Update deployment YAML files with new Docker tag
                    sh """
                    sed -i 's#image: docker.io/ajaycentos/sampleapp:.*#image: docker.io/ajaycentos/sampleapp:${DOCKER_TAG}#' node_app1/deployment.yaml
                    """
                    sh """
                    ls
                    cat node_app1/deployment.yaml
                    """
                    // Check changes and push back to the main branch
                    withCredentials([usernamePassword(credentialsId: 'git_user_token', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {

                    sh """
                    git config user.email "ajaycentos@gmail.com"
                    git config user.name "AjayCentos"
                    git add node_app1/deployment.yaml
                    git commit -m "Update deployment image to ajaycentos/sampleapp:${DOCKER_TAG}"
                    git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/ajaycentos/gke_jenkins_argocd_deployment.git main

                    """
                    }
                }
            }
        }

    }

}


def getDockerTag(){
    def tag = sh (script: 'git rev-parse --short=10 HEAD', returnStdout: true).trim()
    return tag 
}


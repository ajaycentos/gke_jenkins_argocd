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
                    withCredentials([string(credentialsId: 'docker-pwd-only', variable: 'dockerhubpwd')]) {
                       sh "docker login -u ajaycentos -p ${dockerhubpwd}"
                       sh "docker push ajaycentos/sampleapp:${DOCKER_TAG}"
            }          

            }
        }
        // stage('Deploy to k8s'){
        //     steps{
        //         sh "chmod +x changeTag.sh"
        //         sh "./changeTag.sh ${DOCKER_TAG}"
        //         sshagent(['vm3-k8s-privatekey']) {
        //             sh 'scp -o StrictHostKeyChecking=no node-app-pod.yml ajay@192.168.56.33:/home/ajay/app1'
        //             sh 'ssh -o StrictHostKeyChecking=no -l ajay 192.168.56.33 kubectl apply -f /home/ajay/app1/node-app-pod.yml'

        //         }       
        //     }
        // }
        stage('Deploy to K8s'){
            steps{                    
                    withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'kubeconfig', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                    sh "chmod +x changeTag.sh"
                     sh "./changeTag.sh ${DOCKER_TAG}"
                     sh "cat node-app-pod.yml"
                     sh "kubectl apply -f node-app-pod.yml"
                    }
                }
            }
        }
    }


def getDockerTag(){
    def tag = sh (script: 'git rev-parse --short=10 HEAD', returnStdout: true).trim()
    return tag 
}


pipeline{
    
    agent any
    
    tools {
    maven 'maven'
    }
    
    environment {
    DOCKER_TAG = getVersion()
    }


    stages{
        stage('SOURCE CODE CLONING'){
            steps{
                git 'https://github.com/rhegisan/JENKINS-DOCKER-ANSIBLE-AWS.git'
            }
        }
       
        stage('MAVEN BUILD'){
            steps{
               sh "mvn clean package"
            }
        }
        
        stage('BUILDING DOCKER IMAGE'){
            steps{
               sh "docker build . -t rhegisan/rhegiapp:${DOCKER_TAG}"
            }
        }
        
        stage('PUSH IMAGE TO DOCKERHUB'){
            steps{
                withCredentials([string(credentialsId: 'DockerHub', variable: 'docker-hub-pwd')]) {
                sh "docker login -u rhegisan -p ${docker-hub-pwd}"
                }
                sh "docker push rhegisan/rhegiapp:${DOCKER_TAG}"
            }
        }
     
     stage('DOCKER CONTAINER THROUGH ANSIBLE'){
            steps{
               ansiblePlaybook credentialsId: 'ssh', disableHostKeyChecking: true, extras: " -e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'production.inv', playbook: 'docker_container.yml'
            }
        }
       
}
}

def getVersion(){
    def tag = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return tag
}




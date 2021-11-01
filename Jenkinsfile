pipeline {
    agent any
    environment { 
        registry = "kelvinbun22/belajar-cicd" 
        registryCredential = 'dockerHub' 
        dockerImage = '' 
        HOST = "10.1.1.13"
    }
    tools{
        maven 'maven'
    }
    stages {
        stage ('Checkout Git'){
            steps {
            checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'github', url: 'https://github.com/kelvinbun64/jenkins-docker.git']]])
          }
        }
        stage ('Build Maven'){
            steps {
                script{
                    sh "mvn -Dmaven.test.failure.ignonre=true clean package"
                }
            }
        }
        stage ('Build docker image'){
            steps{
                script{
                    sh 'docker build -t $registry":$BUILD_NUMBER" .'
                }
            }
        }
        stage ('Push docker To Repository'){
            steps{
                script{
                    docker.withRegistry( '', registryCredential ) {
                        sh 'docker push $registry:$BUILD_NUMBER '
                    }
                }
            }
        }
        stage ('Clean Image'){
            steps{
                script{
                    sh 'docker rmi $registry:$BUILD_NUMBER '
                }
            }
        }
        stage ('Deploy'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'ssh-to-server', variable: 'password')]) {
                        sh 'sshpass -p "$password" ssh -o "StrictHostKeyChecking no" -t kbun@$HOST "docker run --name testing -d -p 8080:8080 $registry:$BUILD_NUMBER"'
                    }
                }
            }
        }
    }
} 

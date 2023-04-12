pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockid')
    }

    stages{
        
        stage('Build') {
            steps {
                script {
                    checkout scm
                    sh 'pwd'
                    sh 'ls'
                    sh 'rm -rf *.war'
                    sh 'jar -cvf NewSurvey.war -C src/main/webapp/ .'
                    // sh 'echo ${BUILD TIMESTAMP}'
                    // sh 'pwd'
                    sh 'docker build -t ramiyappan/studentsurvey .'
                    sh 'pwd'
                    // sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
               }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                script {
                    // sh 'echo ${BUILD_TIMESTAMP}'
                    sh 'docker push ramiyappan/studentsurvey:latest'
                }
            }
        }
       
        stage('Deploying Rancher to single node') {
            steps {
                script{
                sh 'kubectl set image deployment/hw2-cluster-deployment container-0=dipakmeher51/studentsurvey645:'+tag
                }
            }
        }
        
        stage('Deploying Rancher to Load Balancer') {
            steps {
                script{
                    sh 'kubectl set image deployment/hw2-cluster-deploymentlb container-0=dipakmeher51/studentsurvey645:'+tag
                }
            }
        }
    }
}

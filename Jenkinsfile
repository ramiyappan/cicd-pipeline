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
                    // sh 'rm -rf *.war'
                    sh 'jar -cvf NewSurvey.war -C webapp/ .'
                    // sh 'echo ${BUILD TIMESTAMP}'
                    sh 'docker build -t ramiyappan/studentsurvey:$BUILD_NUMBER .'
                    sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
               }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                script {
                    // sh 'echo ${BUILD_TIMESTAMP}'
                    docker.withRegistry('',registryCredential) {
                        def image = docker.build('ramiyappan/studentsurvey:'+tag, '.')
                        docker.withRegistry('',registryCredential) {
                            image.push()
                        }
                    }
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

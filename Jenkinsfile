pipeline {
    agent any
    environment {
        PROJECT_ID = 'mythic-inn-367421'
                CLUSTER_NAME = 'finalcluster'
                LOCATION = 'us-east-1a'
                CREDENTIALS_ID = 'dockid'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build image') {
            steps {
                script {
                    sh 'rm -rf *.war'
                    sh 'jar -cvf StudentSurvey.war -C src/main/webapp .'
                    app = docker.build("ramiyappan/studentsurvey:${env.BUILD_ID}")
                    }
            }
        }
        
        stage('Push image') {
            steps {
                script {
                    withCredentials( \
                                 [string(credentialsId: 'dockid',\
                                 variable: 'dockid')]) {
                        sh "docker login -u ramiyappan -p ${dockid}"
                    }
                    app.push("${env.BUILD_ID}")
                 }
                                 
            }
        }
    
        stage('Deploy to K8s') {
            steps{
                echo "Deployment started ..."
                sh 'ls -ltr'
                sh 'pwd'
                sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', \
                  projectId: env.PROJECT_ID, \
                  clusterName: env.CLUSTER_NAME, \
                  location: env.LOCATION, \
                  manifestPattern: 'deployment.yaml', \
                  credentialsId: env.CREDENTIALS_ID, \
                  verifyDeployments: true])
                }
            }
        }    
}

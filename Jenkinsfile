@NonCPS
def generateTag() {
    return "build-" + new Date().format("yyyyMMdd-HHmmss")  
}

pipeline {
    environment {
        registry = "ramiyappan/studentsurvey"
         registryCredential = 'dockid'
    }
    agent any

    stages{
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ramiyappan/cicd-pipeline.git']]])
            }
        }

        stage('Build') {
            steps {
                script {
                    // sh 'echo ${BUILD_TIMESTAMP}'
                    //tag = generateTag()
                    docker.withRegistry('',registryCredential){
                      def customImage = docker.build("ramiyappan/studentsurvey:")
                   }
               }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    sh 'echo ${BUILD_TIMESTAMP}'
                    docker.withRegistry('',registryCredential) {
                        def image = docker.build('ramiyappan/studentsurvey:', '.')
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
               sh 'kubectl set image deployment/finalcluster container-0=ramiyappan/studentsurvey:'
            }
         }
      }

    // stage('Deploying Rancher to Load Balancer') {
    //    steps {
    //       script{
    //          sh 'kubectl set image deployment/surveyformlb container-0=srivallivajha/survey645:'+tag
    //       }
    //    }
    // }

    }
}

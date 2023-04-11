pipeline {
   environment {
        registry = "ramiyappan/studentsurvey"
        registryCredential = 'dockid'
        TIMESTAMP = new Date().format("yyyyMMdd_HHmmss")
    }
   agent any

   stages {
      stage('Build') {
         steps {
            script{
               sh 'rm -rf *.war'
               sh 'jar -cvf studentSurveyForm.war -C Webcontent .'
               //sh 'echo ${BUILD_TIMESTAMP}'

               docker.withRegistry('https://index.docker.io/v1/', registryCredential){
                  def customImage = docker.build("ramiyappan/studentsurvey:${env.TIMESTAMP}")
               }
            }
         }
      }

      stage('Push Image to Dockerhub') {
         steps {
            script{
               docker.withRegistry('https://index.docker.io/v1/', registryCredential){
                  sh "docker push ramiyappan/studentsurvey:${env.TIMESTAMP}"
               }
            }
         }
      }

      stage('Deploying Rancher to single pod') {
         steps {
            script{
               sh "kubectl set image deployment/student-survey container-0=ramiyappan/studentsurvey:${env.TIMESTAMP}"
            }
         }
      }
      
      stage('Deploying to Rancher using Load Balancer as a service') {
         steps {
            script{
               sh "kubectl set image deployment/student-survey-lb container-0=ramiyappan/studentsurvey:${env.TIMESTAMP}"
            }
         }
      }

      
   }
}

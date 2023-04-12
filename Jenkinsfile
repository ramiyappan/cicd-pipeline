pipeline {
   environment {
        registry = "ramiyappan/newsurvey"
        registryCredential = 'dockid'
        TIMESTAMP = new Date().format("yyyyMMdd_HHmmss")
    }
   agent any

   stages {
      stage('Build') {
         steps {
            script{
               sh 'rm -rf *.war'
               sh 'jar -cvf Survey.war -C src/main/webapp/ .'
               //sh 'echo ${BUILD_TIMESTAMP}'

               docker.withRegistry('https://index.docker.io/v1/', registryCredential){
                  def customImage = docker.build("ramiyappan/newsurvey:${env.TIMESTAMP}")
               }
            }
         }
      }

      stage('Push Image to Dockerhub') {
         steps {
            script{
               docker.withRegistry('https://index.docker.io/v1/', registryCredential){
                  sh "docker push ramiyappan/newsurvey:${env.TIMESTAMP}"
               }
            }
         }
      }

      stage('Deploying Rancher to single pod') {
         steps {
            script{
               sh "kubectl set image deployment/newdeployment container-0=ramiyappan/newsurvey:${env.TIMESTAMP}"
            }
         }
      }
      

      
   }
}

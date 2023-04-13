pipeline {
   environment {
        DOCKERHUB_CREDENTIALS = credentials('dockid')
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
               sh "docker build -t ramiyappan/studentsurvey:${env.TIMESTAMP} ."
               sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
         }
      }

      stage('Push Image to Dockerhub') {
         steps {
            script{
                  sh "docker push ramiyappan/studentsurvey:${env.TIMESTAMP}"
            }
         }
      }

      stage('Deploying Rancher to single pod') {
         steps {
            script{
               sh "kubectl set image deployment/newdeployment container-0=ramiyappan/studentsurvey:${env.TIMESTAMP}"
            }
         }
      }
      
      stage('Deploying to Rancher using Load Balancer as a service') {
         steps {
            script{
               sh 'kubectl rollout restart deploy newdeployment -n default'
            }
         }
      }
      
      post {
	      always {
			   sh 'docker logout'
		   }
	   }    

            
   }
}

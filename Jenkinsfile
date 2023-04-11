@NonCPS
def generateTag() {
  return "build-" + new Date().format("yyyyMMdd-HHmss")
}

pipeline {
  environment {
    registry = "ramiyappan/studentsurvey"
    registryCredential = 'dockid'
  }
  agent any
  
  stages {
    stage("Building the Student Survey Image") {
      steps {
        script {
          checkout scm
          sh 'rm -rf *.war'
          sh 'jar -cvf Survey.war -C Webcontent/ .'
          tag = generateTag()
          docker.withRegistry('',registryCredential){
          def customImage = docker.build("ramiyappan/studentsurvey:"+tag)
          }
        }
      }
    }
    stage("Pushing Image to DockerHub") {
      steps {
        script {
           docker.withRegistry('',registryCredential) {
             def image = docker.build('ramiyappan/studentsurvey:'+tag, '.')
             docker.withRegistry('',registryCredential) {
               image.push()
             }
           }
        }
      }
    }
    stage("Deploying to Rancher as single pod") {
      steps {
        script {
          sh 'kubectl set image deployment/finaldeploy container-0=ramiyappan/studentsurvey:'+tag
      }
    }
    stage("Deploying to Rancher as with load balancer") {
      steps {
        sh 'kubectl set image deployment/loadbal container-0=ramiyappan/studentsurvey:'+tag'
      }
    }
  }
}

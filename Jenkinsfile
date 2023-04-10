pipeline {
  agent any
  environment {
    DOCKERHUB_PASS = credentials('dockid')
  }
  
  stages {
    stage("Building the Student Survey Image") {
      steps {
        script {
          def BUILD_TIMESTAMP = sh(returnStdout: true, script: 'date +%s').trim()
          checkout scm
          sh 'rm -rf *.war'
          sh 'jar -cvf Survey.war -C Webcontent/ .'
          sh 'echo $(BUILD_TIMESTAMP)'
          withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', passwordVariable: 'DOCKERHUB_PASS', usernameVariable: 'DOCKERHUB_USER')]) {
          sh """
          docker login -u ${ramiyappan} -p '${DOCKERHUB_PASS}'
          """
}
          def customImage = docker.build("ramiyappan/studentsurvey:${BUILD_TIMESTAMP}")
        }
      }
    }
    stage("Pushing Image to DockerHub") {
      steps {
        script {
          sh 'docker push ramiyappan/studentsurvey:${BUILD_TIMESTAMP}'
        }
      }
    }
    stage("Deploying to Rancher as single pod") {
      steps {
        sh 'kubectl set image deployment/stusurvey-pipeline stusurvey-lb-ramiyappan/studentsurvey:${BUILD_TIMESTAMP)-n jenkins-pipeline'
      }
    }
    stage("Deploying to Rancher as with load balancer") {
      steps {
        sh 'kubectl set image deployment/studentsurvey-lb studentsurvey-lb-ramiyappan/studentsurvey:${BUILD_TIMESTAMP)-n jenkins-pipeline'
      }
    }
  }
}

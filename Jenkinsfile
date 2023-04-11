pipeline {
  agent any
  environment {
    DOCKERHUB_PASS = credentials('dockid')
  }
  
  stages {
    stage("Building the Student Survey Image") {
      steps {
        script {
          checkout scm
          sh 'rm -rf *.war'
          sh 'jar -cvf Survey.war -C Webcontent/ .'
          sh "docker login -u ramiyappan -password ${DOCKERHUB_PASS}"
          def customImage = docker.build("ramiyappan/studentsurvey")
        }
      }
    }
    stage("Pushing Image to DockerHub") {
      steps {
        script {
          sh 'docker push ramiyappan/studentsurvey'
        }
      }
    }
    stage("Deploying to Rancher as single pod") {
      steps {
        sh 'kubectl set image deployment/finaldeploy container-0=ramiyappan/studentsurvey -n jenkins-pipeline'
      }
    }
    stage("Deploying to Rancher as with load balancer") {
      steps {
        sh 'kubectl set image deployment/loadbal container-0=ramiyappan/studentsurvey -n jenkins-pipeline'
      }
    }
  }
}

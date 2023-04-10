pipeline {
  agent any
  environment {
    DOCKERHUB_PASS = credentials('LOGIN@docker23')
  }
  stages {
    stage("Building the Student Survey Image") {
      steps {
        script {
          checkout scm
          sh 'rm -rf *.war'
          sh 'jar -cvf Survey.war -C WebContent/ .'
          sh 'echo $(BUILD_TIMESTAMP)'
          sh "docker login -u ramiyappan -p ${DOCKERHUB_PASS}"
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

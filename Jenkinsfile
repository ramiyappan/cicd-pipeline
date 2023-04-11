pipeline {
    agent any
 
	environment { 
        DOCKERHUB_PASS = credentials('dockid')
    }
    stages{
    	stage("Building the student survey image"){
    		steps{
    			script{
		    		checkout scm
		    		sh 'ls'
		    		sh 'cd Webcontent/ && jar -cvf Survey.war *'
		    		sh("sudo -S docker build --tag ramiyappan/studentsurvey:${BUILD_TIMESTAMP} .")
		    		sh("echo ${BUILD_TIMESTAMP}")
		    		sh('sudo docker login -u ramiyappan -p \"${DOCKERHUB_PASS}\"')
		    	}
    		}
    	
    	
    	}
    
        stage ("pushing image to dockerhub") {
            steps {
            	script{
                	sh("sudo docker push ramiyappan/studentsurvey:${BUILD_TIMESTAMP}")
                }
            }
        }

		stage("Deploy docker image to the kube cluster"){
            steps{
                sh("kubectl set image deployment/assignment2 container-0=ramiyappan/studentsurvey:${BUILD_TIMESTAMP} -n default")
            }
        }
        

    }
}

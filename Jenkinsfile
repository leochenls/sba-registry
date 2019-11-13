pipeline {
    agent any
    environment {
      	GIT_URL = "git@github.com:leochenls/sba-registry.git"
		GIT_CRED = "28f46d03-3138-4cee-a4ed-c134e7b90240"
		DOCKER_REPO="registry.cn-shanghai.aliyuncs.com/chenls/sba-registry"
		DOCKER_REG="https://registry.cn-shanghai.aliyuncs.com"
		DOCKER_REG_KEY = "832cd49-6635-41r1-9w2c-35d184196de4"
		dockerImage = ''
    }
    stages {
    
    	stage('CheckOut Code'){
         	steps{
            	checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: GIT_CRED, url: GIT_URL]]])
            	}
              }
        stage('Maven Build'){
        	steps{
        	    sh 'mvn clean install -DskipTests'
        	}

        }
        
        stage('Building image') {
	      steps{
	        script {
	           docker.withRegistry( DOCKER_REG, DOCKER_REG_KEY ) {dockerImage = docker.build DOCKER_REPO + ":$BUILD_NUMBER"
	           }
	        }
	      }
	    }
	    stage('Push Image') {
      steps{
        script {
		   docker.withRegistry( DOCKER_REG, DOCKER_REG_KEY ) {
		            dockerImage.push()
		          }
		        }
		      }
		}
		
		stage('Deploy Image to prd') {
      steps{
        script {
        	sh "sed -i 's/{version}/" + BUILD_NUMBER + "/g' deployment.yaml"
	   		sh 'kubectl apply -f deployment.yaml'
		      }
		}
		}
		
		
		stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $DOCKER_REPO:$BUILD_NUMBER"
      }
    }
   }
  

}
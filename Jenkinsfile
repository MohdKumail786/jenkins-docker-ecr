
pipeline {
    agent any
    tools {
	    maven "MAVEN_3"
	    jdk "OracleJDK8"
	}

    environment {
        registryCredential = 'ecr:us-east-1:awscreds'
        appRegistry = "905418477455.dkr.ecr.us-east-1.amazonaws.com/mkashraf071"
        docker_ecr_registry = "https://905418477455.dkr.ecr.us-east-1.amazonaws.com"
	DOCKERHUB_CREDENTIALS =  credentials('dockerhubcredentials')   
	
    }
  stages {
    stage('Fetch code'){
      steps {
        git branch: 'docker', url: 'https://github.com/MohdKumail786/jenkins-docker-ecr.git'
      }
    }


    stage('Test'){
      steps {
        sh 'mvn test'
      }
    }

    stage ('CODE ANALYSIS WITH CHECKSTYLE'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
            post {
                success {
                    echo 'Generated Analysis Result'
                }
            }
        }
    stage('Build Codes using maven') {
       steps {
               sh 'mvn clean install -Dskiptests'

     }
           post {
               success {
                    echo 'Generated Build Result'
                       }
                }
    
    }

    stage('Build App Image') {
       steps {
       
         script {
                dockerImage = docker.build( appRegistry + ":$BUILD_NUMBER", "./Docker-files/app/multistage/")
             }

     }
    
    }

    stage('Upload App Image') {
          steps{
            script {
              docker.withRegistry( docker_ecr_registry, registryCredential ) {
                dockerImage.push("$BUILD_NUMBER")
                dockerImage.push('latest')
              }
            }
          }
     }
	  
    stage('Login to Docker Hub') {         
      steps{                            
	sh 'echo $DOCKERHUB_CREDENTIALS_PSW | sudo docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'                 
	echo 'Login Completed'                
      }           
    }   

   stage('Push Image to Docker Hub') {         
    steps{                            
     sh 'sudo docker push mkashraf071/jenkin-dockerhub:$BUILD_NUMBER'           
     echo 'Push Image Completed'       
    }            
}  

  }
}





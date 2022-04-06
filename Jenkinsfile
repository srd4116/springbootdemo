
//@Library('mpl') _

//MPLPipeline {}

pipeline {
  agent any //{ label 'labelname' }
    tools{
	jdk 'JAVA_HOME'
	maven 'Local_maven'		
     }
  options {
    timeout(time: 5, unit: 'MINUTES')
   // timestamps()
    buildDiscarder(logRotator(numToKeepStr: '2'))
  }  
 environment {
 
     BUILDSERVER_WORKSPACE="${WORKSPACE}"	
    // BUILD_NO=sh(returnStdout:true,script:"echo ${GIT_BRANCH}_${BUILD_NUMBER} | sed 's/\\//_/g'").trim()
     
 }
  stages {  
   stage('JENKINS PATH') {  
      steps {
       echo "Server is ready"
      script{
	      sh '''
	        echo "JENKINPATH IS: ${PATH}"
	    
	      sh '''
           // br_name=sh(script:"echo ${BRANCH_NAME}|tr '/' '_' ",returnStdout:true).trim()
     		 def buildid=env.BUILD_ID
     		 echo "Build ID IS"+buildid    
    		 currentBuild.displayName="#"+env.BUILD_ID
		}
      // sh 'git clean -dfx'
      }
    }
   stage('Clean Workspace') {
      steps {
          sh 'mvn clean'          
       	  echo "Cleaning Workspace Done"        
       }

      } 
  stage('Check Out') {
      steps{
    	 checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'b407cb41-e3b3-44a1-80be-babe2e1334e5', url: 'https://github.com/kpreddy2025/spring-mvc-jenkins.git']]])
    	 echo "Code Check Out Done" 
    } 

   }          
    stage('Build') {
      steps {       
      	 sh 'mvn clean compile install'
         echo "Build Done"        
      }
    }
    stage('Test') {
      steps {
      echo "Test"
       
      }
    }
    stage('Code Analysis') {
   	 	environment {
       		 scannerHome = tool 'sonarqube-1'
       		  ORGANIZATION = "spring-mvc-jenkins-dev"
   			  PROJECT_NAME = "sonaruser"
    	}
      steps {
      withSonarQubeEnv('sonarqube-1') {
    			sh ''' ${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=sonaruser -Dsonar.java.binaries=target/classes -Dsonar.sources=.
    			'''
		}
		timeout(time: 5, unit: 'MINUTES') {
            waitForQualityGate abortPipeline: true
        }
       echo "Code Analysis Done"
      
      }
    }
    stage ('Archive artifacts for ServiceApp'){
          steps{
              archiveArtifacts '**/**/*.jar'
          }
    }
    stage('Deploy') {
      steps {
        echo "Deploy Done"
       
      }
    }
  }

  post {
    success {
      echo "SUCCESS"
    }
    failure {
      echo "FAILURE"
    }
    changed {
      echo "Status Changed: [From: $currentBuild.previousBuild.result, To: $currentBuild.result]"
    }
    always {
      script {
        def result = currentBuild.result
        if (result == null) {
          result = "SUCCESS"
        }
      }
    }
  }
}


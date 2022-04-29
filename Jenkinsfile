
//@Library('mpl') _

//MPLPipeline {}

pipeline {
  agent any //{ label 'labelname' }
    tools{
	jdk 'JAVA_HOME'
	maven 'Local_maven'		
     }
  options {
    timeout(time: 15, unit: 'MINUTES')
   // timestamps()
    buildDiscarder(logRotator(numToKeepStr: '2'))
  }  
 environment {
 
     BUILDSERVER_WORKSPACE="${WORKSPACE}"	
    // BUILD_NO=sh(returnStdout:true,script:"echo ${GIT_BRANCH}_${BUILD_NUMBER} | sed 's/\\//_/g'").trim()
     
 
	NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "localhost:8081"
        NEXUS_REPOSITORY = "maven2_upload"
        NEXUS_CREDENTIAL_ID = "nexusjenkins"
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
       		 scannerHome = tool 'sonar_name'
       		 
    	}
      steps {
	      echo "Print Sonar Done"
      //withSonarQubeEnv('sonarname') {
    			//sh ''' ${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=sonar1 -Dsonar.java.binaries=target/classes -Dsonar.sources=.
    			//'''
		//}
		//timeout(time: 15, unit: 'MINUTES') {
            //waitForQualityGate abortPipeline: true
       // }
       //echo "Code Analysis Done"
      
       }	 
     
    }
    stage ('Archive artifacts for ServiceApp'){
	    steps {
      // artifactId = "springbootdemo"//pringbootdemoreadMavenPom().getArtifactId()
      //version = "0.0.1-SNAPSHOT"//readMavenPom().getVersion()
       // groupId = "com.howtodoinjava.demo"//readMavenPom().getGroupId()
        
       // echo "*** File: springbootdemo}, ${version}, ${groupId}"
        nexusArtifactUploader (
            nexusVersion: 'nexus3', 
            protocol: 'http', 
            nexusUrl: 'localhost:8081', 
            groupId: "com.howtodoinjava.demo", 
            version: "0.0.1-SNAPSHOT", 
            repository: 'maven2_upload', 
            credentialsId: 'nexusjenkins', 
            artifacts: [
                [artifactId: "spring-mvc-jenkins",  classifier: '', file: "target/spring-mvc-jenkins.war", type: 'war'],
                [artifactId: "spring-mvc-jenkins", classifier: '', file: 'pom.xml', type: 'pom']
            ]
        )
	 }
    }
    stage('Deploy') {
      steps {
        echo "Deploy Done"
       
NEXUS_BASE_URL="http://localhost:8081"
REPOSITORY="maven2_upload"
GROUP_ID="com.howtodoinjava.demo"
ARTIFACT_ID="spring-mvc-jenkins"
VERSION="0.0.1-SNAPSHOT"
LOCAL_FILE="spring-mvc-jenkins"

NEXUS_RESOLVE_URL="${NEXUS_BASE_URL}artifact/maven/resolve?g=${GROUP_ID}a=${ARTIFACT_ID}&r=${REPOSITORY}&v=${VERSION}"
REPOSITORY_LOCAL_PATH='curl -s "${NEXUS_RESOLVE_URL}" | xmllint --xpath "//artifact-resolution/data/repositoryPath/text()" -'
ARTIFACT_DOWNLOAD_URL="${NEXUS_BASE_URL}repositories/${REPOSITORY}/content${REPOSITORY_LOCAL_PATH}"
curl -o "${ARTIFACT_DOWNLOAD_URL}"
	      
	      
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
    //always {
      //script {
        //def result = currentBuild.result
        //if (result == null) {
          //result = "SUCCESS"
        //}
      //}
    //}
  //}
  }
  }


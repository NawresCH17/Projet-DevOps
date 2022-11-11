pipeline {


    agent any
    stages{
       stage('Checkout GIT'){
         steps {
             echo 'Pulling ...';
              git branch: 'nawres',
              url : 'https://github.com/NawresCH17/Achat-Devops.git'
         } 

       }
       stage('Testing maven'){
            steps {
                sh """mvn -version """
            }
       }
       stage('MVN CLEAN'){
            steps {
                sh 'mvn clean'
            }
       }
       stage('MVN COMPILE'){
            steps {
                sh 'mvn compile'
            }
       }
	   stage('SonarQube analysis 1') {
            steps {
                sh 'mvn sonar:sonar -Dsonar.login=admin -Dsonar.password=sonar'
            }
       }
       stage('JUnit and Mockito Test'){
            steps{
                script
                {
                    if (isUnix())
                    {
                        sh 'mvn --batch-mode test'
                    }
                    else
                    {
                        bat 'mvn --batch-mode test'
                    }
                }
            }        
       }        
       stage('MVN Nexus'){
            steps {
                sh 'mvn deploy'
            } 
       }         
       stage('Building image docker-compose') {
          steps {
              sh "docker-compose up -d"
          }
       }
       stage('Build image') {
          steps {
            sh "docker build -t nawreschouari/achat_devops ."
       	  }
       }	
 	   stage('Push image') {
 	    steps {
 	       withDockerRegistry([ credentialsId: "dockerHub", url: "" ]) {
 			sh "docker push nawreschouari/achat_devops"
        	}
             }
       }
       stage('Cleaning up') {
 	    steps {
 	       withDockerRegistry([ credentialsId: "dockerHub", url: "" ]) {
 			
        	 sh "docker rmi -f nawreschouari/achat_devops"
        	}
             }
        }
   }
}
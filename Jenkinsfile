pipeline {
  agent any
  tools { 
        maven 'Maven_3_5_2'  
    }
   stages{
    stage('Compile and SAST Scanning') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=natee-securityguru -Dsonar.organization=natee-securityguru -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=6a1187bc7577cd315ff682c540405c5ec144dfaa'
			}
    }

	stage('SCA Scanning') {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }

	stage('Build Image') { 
            steps { 
               withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
                 script{
                 app =  docker.build("asg")
                 }
               }
            }
    }

	stage('Push Image') {
            steps {
                script{
                    docker.withRegistry('https://145988340565.dkr.ecr.us-west-2.amazonaws.com', 'ecr:us-west-2:aws-credentials') {
                    app.push("latest")
                    }
                }
            }
    	}
	    
  }
}

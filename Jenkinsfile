pipeline {
  agent any
  tools { 
        maven 'Apache-MAVEN'  
    }
   stages{
    stage('Compile and SAST Scanning') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=napasornss -Dsonar.organization=napasornss -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=7d4e4d14cc0c48a50aa34461f6ecf89d619c7911'
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
                    docker.withRegistry('https://637423639903.dkr.ecr.ap-southeast-1.amazonaws.com/asg', 'ecr:ap-southeast-1:aws-credentials') {
                    app.push("latest")
                    }
                }
	    }
    }    
  }
}

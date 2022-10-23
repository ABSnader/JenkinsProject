pipeline{
agent any
  tools {
     jdk 'JAVA_HOME'
     maven 'M2_HOME'
  }
  	  environment {
        
        DOCKERHUB_CREDENTIALS = credentials('DockerHubID')
    }
        
        stages{
		
                   stage('docker compose'){
                             steps{
                                 script{
                                    sh 'docker-compose up -d'
                                 }
                             }
                         }

                         
                 stage('Build Maven Spring'){
                             steps{
                                sh 'mvn clean install '
                             }
                         }
                        
			    stage('Build docker image'){
                             steps{
                                 script{
                                    sh 'docker build -t $DOCKERHUB_CREDENTIALS_USR/springprojet .'
                                 }
                             }
                         }
	

                stage('Testing process') {
                              steps {
                               script {
                                sh 'echo "Test is processing ...."'
                                sh 'mvn clean test'
                               }

                              }

                            }

              stage('Quality Gate Status Check'){
                  steps{
                      script{
			      withSonarQubeEnv('sonar') {
			      sh "mvn compile sonar:sonar"
                       	     	}
			      timeout(time: 1, unit: 'HOURS') {
			      def qg = waitForQualityGate()
				      if (qg.status != 'OK') {
					   error "Pipeline aborted due to quality gate failure: ${qg.status}"
				      }
                    		}
		    	    sh "mvn clean install"

                 	}
               	 }
              }
		stage("Maven Build") {
            steps {
                script {
                    sh "mvn package -DskipTests=true"
                }
            }
        }
      
		 		 stage('Docker login') {
    	
                                         steps {
                                          sh 'echo "login Docker ...."'
                   	sh 'docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW'
                               }  }
		 stage('Docker push') {
    	
                 steps {
                      sh 'echo "Docker is pushing ...."'
                     	sh 'docker push $DOCKERHUB_CREDENTIALS_USR/springprojet'
                        }  }

        }

      }

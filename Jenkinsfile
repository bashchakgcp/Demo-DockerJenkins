pipeline {
        agent any
        environment {
            registry = "1212122345/dockerjenkinsdemo"
            registryCredential = 'dockerhub'
            dockerImage = ''
		          PROJECT_ID = 'massive-weft-284917'
 		          CLUSTER_NAME = 'kubedemo'
 		          LOCATION = 'northamerica-northeast1-a'
 		          CREDENTIALS_ID = 'sprint6gke'
        }
		
	    stages {	
		        stage('Scm Checkout') {            
			            steps {
	                  checkout scm
			                  }	
	           }
	           
		   stage('Build') { 
	                steps {
	                  echo "Cleaning and packaging..."
	                  sh 'mvn clean package'		
	                }
	           }
		   
       stage('Test') { 
			        steps {
		          echo "Testing..."
			        sh 'mvn test'
			              }
		   }
		   
       stage('Build Docker Image') { 
			        steps {
	                   script {
	                      dockerImage = docker.build registry + ":$BUILD_NUMBER"
	                   }
	                }
		   }
            
            stage('Deploy Image') {
                steps{
                    script {
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
                    }
                }
            }
	           stage('Deploy to GKE') {
 			            steps{
 				                 echo "Deployment started"
				                 sh 'ls -ltr'
				                 sh 'pwd'
				                 sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
				                 step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID,
				                      clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml',
				                       credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
				                  echo "Deployment Finished"
 	            }
	          }
	    }
	}

pipeline {
    environment{
        dockerImage = ''
    }
    agent any
    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "Maven"
        git "Git"
        jdk "Jdk"
	dockerTool "Docker"
	terraformTool "Terraform"
    }

    stages {
        stage('1.Terraform init') {
            steps {
                	bat 'terraform init' 
				}
            }

    	}


		stage('Quality Gate Check'){
			steps{
				timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
			}
		}

        stage('3.Building image') {
            steps{
                script {
                    echo '---------------------------Building Image----------------------------------'
                    bat 'docker build . -t cloudbased-deployment'
                    echo '---------------------------Image Successfully Build---------------------------------'
		            bat 'docker images'
                }
            }
        }

        stage('4.Deploy image to ECR') {
            steps{
                script{
                    echo '-----------------------------Deploying Image----------------------------------------'
                    docker.withRegistry('https://795361990663.dkr.ecr.us-east-1.amazonaws.com', 'ecr:us-east-1:aws-credentials') {
                        bat 'docker push 795361990663.dkr.ecr.us-east-1.amazonaws.com/cloudbased-deployment:latest'
                        echo '-------------------------Image Successfully pushed--------------------------------'
                    }
                } 
            }
        }
	}

}

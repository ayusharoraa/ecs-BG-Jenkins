pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="990743565507"
        AWS_DEFAULT_REGION="us-west-1" 
	CLUSTER_NAME="ECR-node"
	SERVICE_NAME="ECS-jenk"
	TASK_DEFINITION_NAME="ecs-jenkins-Far:1"
	DESIRED_COUNT="1"
        IMAGE_REPO_NAME="jenkins-hub"
        IMAGE_TAG="${env.BUILD_ID}"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
	registryCredential = "ecs-deploy"
    }
   
    stages {

    // Tests
    stage('Unit Tests') {
      steps{
        script {
          sh 'npm install'
	  
        }
      }
    }
        
    // Building Docker images
    stage('Building imagess') {
      steps{
        script {
          dockerImage = sudo docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
		 docker.withRegistry("https://" + REPOSITORY_URI, "ecr:${AWS_DEFAULT_REGION}:" + registryCredential) {
                    	dockerImage.push()
                	}
         }
        }
      }
      
    stage('Deploy') {
     steps{
            withAWS(credentials: registryCredential, region: "${AWS_DEFAULT_REGION}") {
                script {
			sh './script.sh'
                }
            } 
        }
      }      
      
    }
}


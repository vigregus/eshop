def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]

pipeline {

    agent {
	    node {
		 label 'master'
	    }
    }

	// tools {
    //     // jdk 'OracleJDK11'
	//     // maven 'MAVEN3'	
		
    // }

    environment {
        registry = "408937627166.dkr.ecr.eu-west-1.amazonaws.com/"
        registryCredential = 'dockerhub'
        
        AWS_ACCOUNT_ID = "408937627166"
        IMAGE_TAG = "latest"
        IMAGE_REPO_NAME = "frontend"
        AWS_DEFAULT_REGION = "eu-west-1"
    }

    stages{


        
        stage('Building frontend image') {
            steps{
              script {
                checkout scm
                dir('src/frontend'){  
                    sh 'docker build . -t vigregus/frontend:$BUILD_NUMBER'
                    //dockerImage = docker.build + "vigregus/frontend:$BUILD_NUMBER"
                } 
              }
            }
        }

        // // Uploading Docker images into AWS ECR
        // stage('Pushing to ECR') {
        //     steps{ 
        //         script {
        //             sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
        //             sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        //         }
        //     }
        // }

        stage('UNIT TEST'){
            steps {
                sh 'echo  UNIT test'
            }
        }

        stage('INTEGRATION TEST'){
            steps {
                sh 'echo INTEGRATION TEST'
            }
        }

        stage ('CODE ANALYSIS WITH CHECKSTYLE'){
            steps {
                sh 'echo CODE ANALYSIS WITH CHECKSTYLE'
            }
            post {
                success {
                    echo 'Generated Analysis Result'
                }
            }
        }

        stage('CODE ANALYSIS with SONARQUBE') {

            environment {
                scannerHome = tool 'sonar4.7'
            }

            steps {
                sh 'CODE ANALYSIS with SONARQUBE'


                }
        }
        
        
        
        
        stage('Deploy frontend Image') {
          steps{
            script {
                // //sh 'aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com'
                // docker.withRegistry('https://${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com', 'ecr:${AWS_DEFAULT_REGION}:aws-credentials') {
                //   app.push("$BUILD_NUMBER")
                //   app.push("latest")
                // }
              //dockerImage.push("$BUILD_NUMBER")
              //dockerImage.push('latest')  
            //    docker.withRegistry('https://${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com', 'ecr:${AWS_DEFAULT_REGION}:aws-credentials') {
            //         app.push("${env.BUILD_NUMBER}")
            //         app.push("latest")
            //     }
               withDockerRegistry([ credentialsId: "dockerhubcreds", url: "" ]){
                 sh 'docker push vigregus/frontend:$BUILD_NUMBER'
               }
            }
          }
        }
	
        

        



        

 
    }
    post {
        always {
            echo 'Slack Notifications.'
            // slackSend channel: '#jenkins-cicd',
            //     color: COLOR_MAP[currentBuild.currentResult],
            //     message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
            }        

        }

    
}
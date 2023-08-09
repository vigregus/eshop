def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]

pipeline {

    agent {
	    node {
		 label 'docker-agent'
	    }
    }

	tools {
        jdk 'OracleJDK11'
	    maven 'MAVEN3'	
		
    }

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
                dir('src/frontend'){  
                    dockerImage = docker.build registry + "frontend:$BUILD_NUMBER"
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
               docker.withRegistry('https://${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com', 'ecr:${AWS_DEFAULT_REGION}:aws-credentials') {
                    app.push("${env.BUILD_NUMBER}")
                    app.push("latest")
                }
            }
          }
        }
	
        

        stage('UNIT TEST'){
            steps {
                sh 'mvn test'
            }
        }

        stage('INTEGRATION TEST'){
            steps {
                sh 'mvn verify -DskipUnitTests'
            }
        }

        stage ('CODE ANALYSIS WITH CHECKSTYLE'){
            steps {
                sh 'mvn checkstyle:checkstyle'
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
                withSonarQubeEnv('sonar-pro') {
                    sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile-repo \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                }

                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
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
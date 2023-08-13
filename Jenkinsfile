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


        
        stage('Building images') {
            parallel {
                stage('frontend') {
                    stages{
                        stage('frontend') {
                            steps{
                                script {
                                    dir('src/frontend'){  
                                      sh 'docker build . -t vigregus/frontend:$BUILD_NUMBER'
                                
                                    } 
                                }
                            }
                        }
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

                            steps {
                                sh 'echo CODE ANALYSIS with SONARQUBE'
                            }
                            post {
                                success {
                                    echo 'Generated Analysis Result'
                                }
                            }    
                        }
                        stage('Push frontend Image') {
                            steps{
                                script {
                                
                                withDockerRegistry([ credentialsId: "dockerhubcreds", url: "" ]){
                                    sh 'docker push vigregus/frontend:$BUILD_NUMBER'
                                }
                                }
                            }
                        }
                    }
                }
                // stage('loadgenerator') {
                //     stages{    
                //         stage('loadgenerator') {
                //             steps{
                //                 script {
                //                     dir('src/loadgenerator'){  
                //                     sh 'docker build . -t vigregus/loadgenerator:$BUILD_NUMBER'
                //                 //dockerImage = docker.build + "vigregus/frontend:$BUILD_NUMBER"
                //                     } 
                //                 }
                //             }
                //         }
                //         stage('UNIT TEST'){
                //             steps {
                //                 sh 'echo  UNIT test'
                //             }
                //         }

                //         stage('INTEGRATION TEST'){
                //             steps {
                //                 sh 'echo INTEGRATION TEST'
                //             }
                //         }

                //         stage ('CODE ANALYSIS WITH CHECKSTYLE'){
                //             steps {
                //                 sh 'echo CODE ANALYSIS WITH CHECKSTYLE'
                //             }
                //             post {
                //                 success {
                //                     echo 'Generated Analysis Result'
                //                 }
                //             }
                //         }

                //         stage('CODE ANALYSIS with SONARQUBE') {

                //             steps {
                //                 sh 'echo CODE ANALYSIS with SONARQUBE'
                //             }
                //             post {
                //                 success {
                //                     echo 'Generated Analysis Result'
                //                 }
                //             }    
                //         }
                //         stage('Push loadgenerator Image') {
                //             steps{
                //                 script {
                                
                //                 withDockerRegistry([ credentialsId: "dockerhubcreds", url: "" ]){
                //                     sh 'docker push vigregus/loadgenerator:$BUILD_NUMBER'
                //                 }
                //                 }
                //             }
                //         }
                //     }
                // }
                // stage('cartservice') {
                //     stages{    
                //         stage('checkoutservice') {
                //             steps{
                //                 script {
                //                     dir('src/checkoutservice'){  
                //                     sh 'docker build . -t vigregus/checkoutservice:$BUILD_NUMBER'
                //                 //dockerImage = docker.build + "vigregus/frontend:$BUILD_NUMBER"
                //                     } 
                //                 }
                //             }
                //         }
                //         stage('UNIT TEST'){
                //             steps {
                //                 sh 'echo  UNIT test'
                //             }
                //         }

                //         stage('INTEGRATION TEST'){
                //             steps {
                //                 sh 'echo INTEGRATION TEST'
                //             }
                //         }

                //         stage ('CODE ANALYSIS WITH CHECKSTYLE'){
                //             steps {
                //                 sh 'echo CODE ANALYSIS WITH CHECKSTYLE'
                //             }
                //             post {
                //                 success {
                //                     echo 'Generated Analysis Result'
                //                 }
                //             }
                //         }

                //         stage('CODE ANALYSIS with SONARQUBE') {

                //             steps {
                //                 sh 'echo CODE ANALYSIS with SONARQUBE'
                //             }
                //             post {
                //                 success {
                //                     echo 'Generated Analysis Result'
                //                 }
                //             }    
                //         }
                //         stage('Push checkoutservice Image') {
                //             steps{
                //                 script {
                                
                //                 withDockerRegistry([ credentialsId: "dockerhubcreds", url: "" ]){
                //                     sh 'docker push vigregus/checkoutservice:$BUILD_NUMBER'
                //                 }
                //                 }
                //             }
                //         }
                //     }
                // }
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
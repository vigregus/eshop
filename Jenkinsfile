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
        registry = '408937627166.dkr.ecr.eu-west-1.amazonaws.com/frontend'
        registryCredential = 'Aws-Jenkins'
        
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
                        // stage('checkout'){
                        //     steps {

                        //          script {
                                        
                        //                     timeout(time:10, unit: 'MINUTES') {
                        //                     waitUntil {//do checkout until it is successful
                        //                     try {
                        //                         checkout([$class: 'GitSCM', branches: [[name: main]], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'CleanBeforeCheckout']], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github_jenkins', url: 'https://github.com/vigregus/eshop.git']]])
                        //                         true
                        //                     } catch(err) {
                        //                         echo "Failed to checkout from Git. Will try again in 30 sec"
                        //                         sleep(30)
                        //                         false
                        //                     }
                        //                     }
                        //                 }
                        //              }
                        //     }
                        // }
                        stage('checkout'){
                            steps{
                                script{
                                    checkout scm
                                }
                            }
                        }
                                        
                                        
                        
                        stage('frontend') {
                            steps{
                                script {
                                    dir('src/frontend'){  
                                      dockerImage = docker.build registry + ":$BUILD_NUMBER"  
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
                                  dir('src/frontend'){  

                                    docker.withRegistry("https://" + registry, "ecr:" + AWS_DEFAULT_REGION + ":" + registryCredential) {
                                            dockerImage.push()
                                    }
                                  }
                                // withDockerRegistry([ credentialsId: "dockerhubcreds", url: "" ]){
                                //    // sh 'docker push vigregus/frontend:$BUILD_NUMBER'
                                //    sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:$BUILD_NUMBER"
 
                                // }
                                }
                            }
                        }
                        stage('apply to k8s manifest to allow argoCD update Image'){
                            steps {
                                withCredentials([gitUsernamePassword(credentialsId: 'github_jenkins', gitToolName: 'git')]) {
                                        sh 'git config user.email "vigregus@gmail.com"'
                                        sh 'git config user.name "vigregus"'
                                        sh 'git switch main'
                                        sh 'git branch'
                                        sh 'git log -3'
                                        
                                }    
                                sh "sed -i.backup \'s!image: 408937627166.dkr.ecr.eu-west-1.amazonaws.com/frontend:.*!image: 408937627166.dkr.ecr.eu-west-1.amazonaws.com/frontend:$BUILD_NUMBER!g\' release/kubernetes-manifests.yaml"
                                //sh 'cat release/kubernetes-manifests.yaml'
                                withCredentials([gitUsernamePassword(credentialsId: 'github_jenkins', gitToolName: 'git')]) {
                                        
                                        sh 'git add release/kubernetes-manifests.yaml'
                                        sh 'git commit --message=qwe'
                                        sh 'git log -3'
                                        sh 'git push origin main'
                                        sh 'pwd'
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

            echo 'clean workspace'
            sh 'pwd'
            //
            // slackSend channel: '#jenkins-cicd',
            //     color: COLOR_MAP[currentBuild.currentResult],
            //     message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
            }        

        }

    
}
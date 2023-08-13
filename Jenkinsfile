pipeline {

    agent {
	    node {
		 label 'master'
	    }
    }
    environment {
        registry = '408937627166.dkr.ecr.eu-west-1.amazonaws.com/frontend'
        registryCredential = 'Aws-Jenkins'    
        AWS_DEFAULT_REGION = "eu-west-1"
    }

    stages{
        stage('Building images') {
            parallel {
                stage('frontend') {
                    stages{
                        stage('checkout'){
                            steps{
                                script{
                                    checkout scm
                                }
                            }
                        }
                        stage('build frontend') {
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
                                }
                            }
                        }
                        stage('apply to k8s manifest to allow argoCD update Image'){
                            steps {
                                withCredentials([gitUsernamePassword(credentialsId: 'github_jenkins', gitToolName: 'git')]) {
                                    sh 'git config user.email "vigregus@gmail.com"'
                                    sh 'git config user.name "vigregus"'
                                    sh 'git switch main'
                                    sh 'git pull'    
                                }    
                                sh "sed -i.backup \'s!image: 408937627166.dkr.ecr.eu-west-1.amazonaws.com/frontend:.*!image: 408937627166.dkr.ecr.eu-west-1.amazonaws.com/frontend:$BUILD_NUMBER!g\' release/kubernetes-manifests.yaml"

                                withCredentials([gitUsernamePassword(credentialsId: 'github_jenkins', gitToolName: 'git')]) {
                                    sh 'git add release/kubernetes-manifests.yaml'
                                    sh 'git commit --message=qwe'
                                    sh 'git push origin main'
                                }            
                            }
                        }

                    }
                }
                
            }    
             
        }
    }
    post {
        always {
            echo 'clean workspace'
            sh 'pwd'
            }        

        }

    
}
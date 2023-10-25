pipeline {
    agent { label 'jenkins-agent12'}
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    environment {
	    APP_NAME = "register-app-pipeline"
            RELEASE = "1.0.0"
            DOCKER_USER = "ibanoba"
            DOCKER_PASS = 'dockerhubz'
            IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
            IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }
    stages{
        stage("Clean up Workspace"){
                steps {
                cleanWs()
                }        
        }
        stage("Checkout from SCM"){
                steps {
                git branch: 'main', credentialsId: 'githubz', url: 'https://github.com/ibanoba/register-app'
                }
        }
        stage("Build Application"){
                steps {
                    sh "mvn clean package"
                }        
        }
        stage("Test Application"){
                steps {
                    sh "mvn test"
                }
        }
        stage("SonarQube Analysis"){
                steps {
                    script {
                        withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token'){
                        sh "mvn sonar:sonar" }
                            }   
                        }
                }
        stage("Quality Gate"){
                steps {
                    script{
                        waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
                        }
                    }
            }
        stage("Build & Push Docker Image"){
                steps {
                    script{
                        docker.withRegistry('',Docker_PASS) {
                            docker_image = docker.build "${IMAGE_NAME}"    
                        }
                        docker.withRegistry('',Docker_PASS) {
                            docker_image.push("${IMAGE_TAG}") 
                            docker_image.push('latest')    
                            }
                        }
                    }
            }
	stage("Trivy Scan") {
        	steps {
        		script {
	            		sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image ibanoba/register-app-pipeline:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
               }
           }
       }
	stage ('Cleanup Artifacts') {
           steps {
               script {
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
               }
          }
       }
    }
}

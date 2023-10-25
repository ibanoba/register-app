pipeline {
    agent { label 'jenkins-agent'}
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    stages{
        stage("Clean up Workspace"){
                steps {
                CleanWs()
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
    }
}

pipeline{
    agent any
    stages{
        stage("GIT CHECKOUT"){
            steps{
                git branch: 'main', url: 'https://github.com/vtiru05/Jenkins-nexus-code-springboot-or-java-.git'
            }
        }
        stage("MAVEN TEST"){
            steps{
                sh "mvn test"
            }
        }
        stage(" MAVEN INTEGRATION"){
            steps{
                sh "mvn verify -DskipUnitTests"
            }
        }
        stage("MAVEN BUILD"){
            steps{
                sh "mvn clean install"
            }
        }
        stage("CODE CHECK WITH SONARQUBE"){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonarqube_token123') {
                        sh "mvn clean package sonar:sonar"
                    }
                }
            }
        }
        stage("UPLOADING INTO NEXUS"){
            steps{
                script{
                    nexusArtifactUploader artifacts:
                    [
                        [
                            artifactId: 'springboot', 
                            classifier: '', file: 'target/Uber.jar', 
                            type: 'jar'
                            ]
                    ], 
                    credentialsId: 'nexus', 
                    groupId: 'com.example', 
                    nexusUrl: '54.186.152.32:8081/nexus', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: 'DEMO', 
                    version: '1.0.0'
                }
            }
        }
        stage("DOCKER IMAGE BUILD"){
            steps{
                sh "sudo docker image build -t $JOB_NAME:v1.$BUILD_ID . "
                sh "sudo docker image tag $JOB_NAME:v1.$BUILD_ID vigneshv04/$JOB_NAME:v1.$BUILD_ID "
                sh "sudo docker image tag $JOB_NAME:v1.$BUILD_ID vigneshv04/$JOB_NAME:latest"
            }
        }
        stage("APPLICATION TO DOCKERHUB"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'sonarqube_token', variable: 'Docker_hub_cred')]) {
                    sh "sudo docker login -u vigneshv04 -p ${Docker_hub_cred}"
                    sh "sudo docker image push vigneshv04/$JOB_NAME:v1.$BUILD_ID"
                    sh "sudo docker image push vigneshv04/$JOB_NAME:latest"
                 }
                }
            }
        }
    }
}

pipeline{
    agent any
    //agent {
    //}

    tools{
        jdk 'Java17'
        maven 'Maven3'
    }

    environment {
        APP_NAME = "e2e-pipeline-production"
        RELEASE = "1.0.0"
        DOCKER_USER = "1759"
        DOCKER_PASS = "Dockerhub-Access"
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG =  "${RELEASE}-${BUILD_NUMBER}"
    }

    stages{

        stage("Cleanup Workspace"){
            steps{
                cleanWs()
            }
        }

        stage("Checkout from SCM"){
            steps{
                git branch: 'master', credentialsId: 'Github Access', url: 'https://github.com/highdbaba/e2e-pipeline.git'
            }
        }

        stage("Building Application"){
            steps{
                sh "mvn clean package"
            }
        }

        stage("Testing Application"){
            steps{
                sh "mvn clean test"
            }
        }

        stage("SonarQube Test Analysis"){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'SonarQube-Access') {
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }

        stage("Build and Push Docker Image"){
            steps{
                script{
                    docker.withRegistry('', Dockerhub-Access) {
                    docker_image = docker.build "${IMAGE_NAME}"
                } 
                }
            }
        }
    }
}
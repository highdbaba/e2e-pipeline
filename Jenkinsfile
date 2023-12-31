pipeline{
    agent any
    //agent {
    //}

    tools{
        jdk 'Java17'
        maven 'Maven3'
    }

    environment {
        APP_NAME = "e2e-newpipeline-production"
        RELEASE = "1.1.0"
        DOCKER_USER = "1759"
        DOCKER_PASS = "dockerhub"
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
                git branch: 'main', credentialsId: 'Github-Access', url: 'https://github.com/highdbaba/e2e-pipeline.git'
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

        /**stage("SonarQube Test Analysis"){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'SonarQube-Access') {
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }**/

        stage('building image and Push Docker Image') {
            steps {
                script {
                    echo "Building the docker image..."
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        
                        sh 'ls -la'
                        docker_image = docker.build "${IMAGE_NAME}"
                        sh "echo $PASS | docker login -u $USER --password-stdin"
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }
        }

        stage('Test effect of Delete on Docker Image') {//This sectionis optional
            steps {
                script {
                    echo "Deleting old docker image..."
                    sh "docker rmi $IMAGE_NAME:$IMAGE_TAG"
                    sh "docker rmi $IMAGE_NAME:latest"   
                }
            }
        }

        stage('Updating Kubernetes Deployment file') {
            steps {
                script {
                    sh """
                    cat deployment.yml
                    sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yml
                    cat deployment.yml
                    """   
                }
            }
        }

        stage('Push edited deployment manifest file to Git') {
            steps {
                script {
                    sh """
                        git config --global user.name "highdbaba"
                        git config --global user.email "highdbaba@yahoo.com"
                        git add deployment.yml
                        git commit -m "updated image on manifest file"
                    """
                    withCredentials([gitUsernamePassword(credentialsId: 'Github-Access', gitToolName: 'Default')]) {
                        sh "git push https://github.com/highdbaba/e2e-pipeline.git main"   
                    }
                }
            }
        }
    }
}
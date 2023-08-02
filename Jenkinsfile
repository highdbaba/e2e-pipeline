pipeline{
    agent any
    //agent {
    //}

    tools{
        jdk 'Java17'
        maven 'Maven3'
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
                    sh "mvn sonar:sonar"
                }
            }
        }
    }
}
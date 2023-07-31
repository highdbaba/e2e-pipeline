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
                git branch: 'Master', credentialsId: 'github', url: 'https://github.com/highdbaba/e2e-pipeline.git'
            }
        }
    }
}
/* groovylint-disable-next-line CompileStatic */
pipeline {
    agent any
    environment {
        TF_PLAN_FILE = 'terraform.tfplan'
        AWS_ACCESS_KEY_ID     = credentials('AWS').'Access Key ID'
        AWS_SECRET_ACCESS_KEY = credentials('AWS').'Secret Access Key'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', 
                          branches: [[name: 'main']], 
                          doGenerateSubmoduleConfigurations: false, 
                          extensions: [], 
                          submoduleCfg: [], 
                          userRemoteConfigs: [[credentialsId: 'GitHub', 
                                               url: 'git@github.com:erickkendall/pipeline-study.git']]])
            }
        }
        stage('Terraform Init') {
            steps {
                sh 'terraform init'
            }
        }
    }
}

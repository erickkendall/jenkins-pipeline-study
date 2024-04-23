/* groovylint-disable-next-line CompileStatic */
pipeline {
    agent any
    environment {
        TF_PLAN_FILE = 'terraform.tfplan'
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
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

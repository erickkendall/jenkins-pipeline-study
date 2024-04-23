/* groovylint-disable-next-line CompileStatic */
pipeline {
    agent any
    environment {
        TF_PLAN_FILE = 'terraform.tfplan'
        AWS_ACCESS_KEY_ID     = credentials('AWS').'Access Key ID'
        AWS_SECRET_ACCESS_KEY = credentials('AWS').'Secret Access Key'
    }
    stages {
        stage('List Files') {
            steps {
                sh 'ls -l'
            }
        }
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', 
                          branches: [[name: 'main']], 
                          doGenerateSubmoduleConfigurations: false, 
                          extensions: [], 
                          submoduleCfg: [], 
                          userRemoteConfigs: [[credentialsId: 'github', 
                                               url: 'git@github.com:erickkendall/jenkins-pipeline-study.git']]])
            }
        }
        stage('Terraform Init') {
            steps {
                sh 'terraform init'
            }
        }
        stage('Terraform Validate') {
            steps {
                script {
                    def tfValidate = sh(script: 'terraform validate', returnStatus: true)
                    def handleTfValidationResult(int tfValidate) {
                        if (tfValidate != 0) {
                            error 'Terraform validation failed!'
                        }
                    }

                    // ...

                    stage('Terraform Validate') {
                        steps {
                            script {
                                def tfValidate = sh(script: 'terraform validate', returnStatus: true)
                                handleTfValidationResult(tfValidate)
                            }
                        }
                    }
                        error 'Terraform validation failed!'
                    }
                }
            }
        }
        stage('Terraform Plan') {
            steps {
                sh "terraform plan -out=$TF_PLAN_FILE"
            }
        }
        stage('Terraform Apply') {
            steps {
                sh "terraform apply $TF_PLAN_FILE"
            }
        }
    }

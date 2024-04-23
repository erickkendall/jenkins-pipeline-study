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
        stage('Terraform Validate') {
            steps {
                script {
                    int tfValidate = sh(script: 'terraform validate', returnStatus: true)
                    def handleTfValidationResult(int tfValidate) {
                        if (tfValidate != 0) {
                            error 'Terraform validation failed!'
                        }
                    }

                    // ...

                    stage('Terraform Validate') {
                        steps {
                            script {
                                int tfValidate = sh(script: 'terraform validate', returnStatus: true)
                                handleTfValidationResult(tfValidate)
                            }
                        }
                    }
                        error 'Terraform validation failed!'
                    }
                }
            }
        }
    }
}

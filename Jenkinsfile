/* groovylint-disable-next-line CompileStatic */
pipeline {
    agent any
    environment {
        TF_PLAN_FILE = 'terraform.tfplan'
        // AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        // AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM',
                          branches: [[name: '*/main']],
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
                // Run terraform validate
                script {
                    def validateStatus = sh script: 'terraform validate', returnStatus: true
                    if (validateStatus != 0) {
                        error('Terraform validation failed. Exiting...')
                    }
                }
            }
        }
        stage('Terraform Plan') {
            steps {
                script {
                    // Configure AWS credentials
                    withCredentials([[
                        $class: 'AmazonWebServicesCredentialsBinding',
                        accessKeyVariable: AWS_ACCESS_KEY_ID,
                        secretKeyVariable: AWS_SECRET_ACCESS_KEY
                    ]]) {
                        // Run terraform plan
                        sh 'terraform plan -out=$TF_PLAN_FILE'
                    }
                }
            }
        }
    }
}

/* groovylint-disable-next-line CompileStatic */
pipeline {
    agent any
    environment {
        TF_PLAN_FILE          = 'terraform.tfplan'
    }

    // Define global variables for AWS credentials
    def awsAccessKeyId
    def awsSecretAccessKey

    stages {
        stage('Get AWS Credentials') {
            steps {
                script {
                    // Retrieve AWS credentials
                    awsAccessKeyId = credentials('AWS_ACCESS_KEY_ID')
                    awsSecretAccessKey = credentials('AWS_SECRET_ACCESS_KEY')
                }
            }
        }
        stage('Get Timestamp') {
            steps {
                script {
                    // Get current build timestamp
                    def timestamp = currentBuild.startTimeInMillis

                    // Convert timestamp to human-readable format (optional)
                    def date = new Date(timestamp)
                    def formattedDate = date.format('yyyy-MM-dd_HH-mm-ss')

                    echo "Timestamp: ${formattedDate}"
                }
            }
        }
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
                script {
                    // Run terraform validate
                    int validateStatus = sh script: 'terraform validate', returnStatus: true
                    if (validateStatus != 0) {
                        error('Terraform validation failed. Exiting...')
                    }
                }
            }
        }
        stage('Terraform Plan') {
            steps {
                script {
                    // Run terraform plan and capture the result
                    def planOutput = sh(script: 'terraform plan -out=$TF_PLAN_FILE', returnStdout: true).trim()

                    // Check if there are any changes to apply
                    if (planOutput.contains('No changes')) {
                        echo 'No changes to apply. Exiting gracefully.'
                        currentBuild.result = 'SUCCESS'
                        return
                    }

                    // Print the plan output
                    echo "Terraform plan output:"
                    echo "${planOutput}"
                }
            }
        }
        stage('Terraform Apply') {
            steps {
                script {
                    // Check if the plan output contains any changes
                    if (planOutput.contains('No changes')) {
                        echo 'No changes to apply. Skipping Terraform apply.'
                    } else {
                        // Retrieve AWS credentials

                        // Set environment variables
                        env.AWS_ACCESS_KEY_ID = awsAccessKeyId
                        env.AWS_SECRET_ACCESS_KEY = awsSecretAccessKey

                        // Apply Terraform changes
                        sh "terraform apply $TF_PLAN_FILE"
                    }
                }
            }
        }
    }
}

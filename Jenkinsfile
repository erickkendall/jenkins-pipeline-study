/* groovylint-disable-next-line CompileStatic */
pipeline {
    agent any
    environment {
        TF_PLAN_FILE          = 'terraform.tfplan'
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }
    stages {
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
                    // Define a Closure to handle validation status
                    def handleValidationStatus = { int status ->
                        if (status != 0) {
                            echo 'Terraform validation failed. Exiting...'
                            error('Terraform validation failed. Exiting...')
                        } else {
                            echo 'Terraform validation successful.'
                        }
                    }

                    // Run terraform validate
                    int validateStatus = sh script: 'terraform validate', returnStatus: true

                    // Call the Closure to handle validation status
                    handleValidationStatus(validateStatus)
                }
            }
        }
        stage('Set AWS Credentials and Terraform Plan') {
            steps {
                script {
                    // Retrieve AWS credentials
                    def awsAccessKeyId = credentials('AWS_ACCESS_KEY_ID')
                    def awsSecretAccessKey = credentials('AWS_SECRET_ACCESS_KEY')

                    // Set environment variables
                    env.AWS_ACCESS_KEY_ID = awsAccessKeyId
                    env.AWS_SECRET_ACCESS_KEY = awsSecretAccessKey

                    // Run terraform plan
                    def planOutput = sh(script: 'terraform plan -out=$TF_PLAN_FILE', returnStdout: true).trim()
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
                        def awsAccessKeyId = credentials('AWS_ACCESS_KEY_ID')
                        def awsSecretAccessKey = credentials('AWS_SECRET_ACCESS_KEY')

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

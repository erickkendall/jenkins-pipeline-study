/* groovylint-disable-next-line CompileStatic */
pipeline() {
    agent any

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
                                               url: 'git@github.com:erickkendall/pipeline-study.git']]])
            }
        }
    }
}

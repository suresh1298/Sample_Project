pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage ("git") {
            steps {
                git 'https://github.com/suresh1298/Sample_Project'
            }
        }
        stage ("scan") {
            environment {
                scannerHome = tool 'sonar'
           }
           steps {
               withSonarQubeEnv('sonar') {
                   sh "${scannerHome}/bin/sonar-scanner"
               }
           }
        }
        stage ("quality") {
            steps {
               timeout(time: 10, unit: 'MINUTES') {
                   waitForQualityGate abortPipeline: true
               }
           }
        }
        stage ("buld") {
            steps {
                sh 'mvn clean install'
            }
        }
        stage ('nexus') {
            steps {
                nexusArtifactUploader credentialsId: '52fee9db-1d17-4c0f-a51c-be460d324ef0', groupId: 'happy', nexusUrl: '15.206.171.100:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'suresh-release', version: '1.1'
            }
        }
    }
}

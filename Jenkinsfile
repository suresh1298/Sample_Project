pipeline {
    agent any
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
    }
}

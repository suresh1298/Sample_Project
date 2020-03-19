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
                nexusPublisher nexusInstanceId: 'nexus', nexusRepositoryId: 'suresh-release', packages: [[$class: 'MavenPackage', mavenAssetList: [], mavenCoordinate: [artifactId: 'simple-web-app', groupId: 'happy', packaging: 'war', version: '1.0']]]
            }
        }
    }
}

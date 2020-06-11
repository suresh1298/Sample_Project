pipeline {
    agent { label 'jenkins_slave' } 
    tools {
        maven 'maven'
    }
    stages {
        stage ("git") {
            steps {
                git credentialsId: '7778fd25-578d-48df-b454-17fe5ca8baa0', url: 'https://github.com/suresh1298/Sample_Project'
            }
        }
        stage ("scan") {
            environment {
                scannerHome = tool 'sonar'
            }
            steps {
                withSonarQubeEnv('sonarQube') {
                   sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
        stage ("quality") {
            steps {
               timeout(time: 1, unit: 'MINUTES') {
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
                nexusArtifactUploader artifacts: [[artifactId: 'simple-web-app', classifier: '', file: 'target/simple-web-app.war', type: 'war']], credentialsId: '5b2035fb-2986-44bb-8ff1-8c433fa1d996', groupId: 'happy', nexusUrl: '34.237.52.252:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'http://34.237.52.252:8081/repository/sample_release/', version: '1.1'
            }
        }
        stage ("deploy") {
            steps {
                sh "sudo cp target/*.war /opt/tomcat/webapps"
            }
        }
    }
}

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
                nexusPublisher nexusInstanceId: 'nexus', nexusRepositoryId: 'suresh-release', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/simple-web-app.war']], mavenCoordinate: [artifactId: 'simple-web-app', groupId: 'happy', packaging: 'war', version: '${VERSION}']]]
            }
        }
        stage ("deploy") {
            steps {
                sh "sudo cp target/*.war /opt/tomcat/webapps"
            }
        }
    }
    post {
        success {
            emailext (
                to: 'reddysuresh1298@gmail.com',
                subject: "JOB: ${env.JOB_NAME} - SUCCESS",
                body: "JOB SUCCESS - \"${env.JOB_NAME}\" Build No: ${env.BUILD_NUMBER}\n\nClick on the below link to view the logs:\n ${env.BUILD_URL}\n"
            )
        }
        failure {
            emailext (
                to: 'reddysuresh1298@gmail.com',
                subject: "JOB: ${env.JOB_NAME} - FAILURE",
                body: "JOB FAILURE - \"${env.JOB_NAME}\" Build No: ${env.BUILD_NUMBER}\n\nClick on the below link to view the logs:\n ${env.BUILD_URL}\n"
            )
        }
    }
}

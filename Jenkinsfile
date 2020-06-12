pipeline {
    agent { label 'jenkins_slave' } 
    tools {
        maven 'maven'
    }
    stages {
        stage ("scm checkout") {
            parallel {
               stage ("git") {
                   steps {
                       node ("jenkins_slave") {
                           git credentialsId: '7778fd25-578d-48df-b454-17fe5ca8baa0', url: 'https://github.com/suresh1298/Sample_Project'
                       }
                   }
               }
               stage ("null") {
                   steps {
                       node ("jenkins_slave") {
                           sh "echo 'suresh'"
                       }
                   }
               }
               stage ("print") {
                   steps {
                       sh "echo 'print'"
                   }
               }
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
                node ("jenkins_slave") {
                    sh 'mvn clean install'
                }
            }
        }
        stage ('nexus') {
            steps {
               nexusPublisher nexusInstanceId: 'nexus', nexusRepositoryId: 'sample_release', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '.war', filePath: 'target/simple-web-app.war']], mavenCoordinate: [artifactId: 'simple-web-app', groupId: 'happy', packaging: 'war', version: '1.3']]]
            }
        }
        stage ("deploy") {
            steps {
                sh "sudo cp target/*.war /opt/tomcat/webapps"
            }
        }
    }
}

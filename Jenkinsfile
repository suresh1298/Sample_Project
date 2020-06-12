pipeline {
    agent { "setup" }
    tools {
        maven 'maven'
    }
    stages {
        stage ("scm checkout") {
            parallel {
               stage ("git") {
                   steps {
                       node ("setup") {
                           git credentialsId: 'aa24c8e4-34ac-4b9b-8fe8-d76c8ac0437b', url: 'https://github.com/suresh1298/Sample_Project'
                       }
                   }
               }
               stage ("null") {
                   steps {
                       node ("setup") {
                           sh "echo 'suresh'"
                       }
                   }
               }
               stage ("print") {
                   steps {
                       node ("setup") {
                           sh "echo 'print'"
                       }
                   }
               }
            }
        }
        stage ("scan") {
            environment {
                scannerHome = tool 'sonar'
            }
            steps {
                node ("sonar_slave") {
                    withSonarQubeEnv('sonarQube') {
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }
        stage ("quality") {
            steps {
                node ("sonar_slave") {
                    timeout(time: 1, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true   
                    }
                }
           }
        }
        stage ("buld") {
            steps {
                node ("setup") {
                    sh 'mvn clean install'
                }
            }
        }
        stage ('nexus') {
            steps {
                node ("setup") {
                    nexusPublisher nexusInstanceId: 'nexus', nexusRepositoryId: 'sample_release', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '.war', filePath: 'target/simple-web-app.war']], mavenCoordinate: [artifactId: 'simple-web-app', groupId: 'happy', packaging: 'war', version: '1.4']]]
                }
            }
        }
        stage ("deploy") {
            steps {
                node ("setup") {
                    sh "sudo cp target/*.war /opt/tomcat/webapps"
                }
            }
        }
    }
}

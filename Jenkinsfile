pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage ("git scm") {
            steps {
                script {
                    if (env.branch_name == 'master') {
                        git 'https://github.com/suresh1298/Sample_Project'
                    } else {
                        sh 'echo this is not from master branch'
                    }    
                }
            } 
        }
        stage ("sonar") {
            environment {
                scannerHome = tool 'sonarscanner'
            }
            steps {
                script {
                    if (env.branch_name == 'master') {
                        withSonarQubeEnv('sonarqube') {
                            sh "${scannerHome}/bin/sonar-scanner"
                        }
                    } else {
                        sh 'echo this is not from master'
                    }
                }
            }
        }
        stage ("quality check") {
            steps {
                script {
                    if (env.branch_name == 'master') {
                        timeout(time: 10, unit: 'MINUTES') {
                            waitForQualityGate abortPipeline: true
                        }
                        
                    } else {
                        sh 'echo this is not from master'
                    }
                }
            }
        }
        stage ('maven build') {
            steps {
                script {
                    if (env.branch_name == 'master') {
                        sh 'mvn clean install'
                    } else {
                        sh 'echo this is not from master'
                    }
                }
            }
        }
        stage ('nexus') {
            steps {
                script {
                   if (env.branch_name == 'master') {
                       nexusArtifactUploader artifacts: [[artifactId: 'happy', classifier: '', file: 'target/simple-web-app.war', type: 'war']], credentialsId: '59cfd18f-873c-4852-be70-8b4e8b5850d1', groupId: 'org.mitre', nexusUrl: '15.206.70.120:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'suresh-release', version: '2.6'
                    } else {
                        sh 'echo this is not from master'
                    }
                }
            }
        }
        stage ('tomcat') {
            steps {
                script {
                   if (env.branch_name == 'master') {
                       sh "sudo cp target/*.war /opt/tomcat/webapps"
                   } else {
                       sh 'echo this is not from master'
                   }
                }
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

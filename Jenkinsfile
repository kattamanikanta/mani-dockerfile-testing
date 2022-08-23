/* @Library("mylibs") _
pipeline {
  agent any
  tools {
    maven 'maven2'
  }
  stages{
    stage("Maven Build"){
      steps{
        sh "mvn clean package"
      }
    }
    stage("Deploy To Dev"){
      steps{
        tomcatDeploy("tomcat-dev","ec2-user",["172.31.13.89","172.31.13.89"])
      }
    }
  }
}
*/
pipeline {
    agent any
    options {
         buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '30', numToKeepStr: '5')
        }
    environment {
        PATH = "/usr/share/maven:$PATH"
    }
    stages {
        stage('git checkout') {
            steps {
                git credentialsId: 'Git-hub', url: 'https://github.com/kattamanikanta/mani-myapps'
            }
        }
        stage('maven build') {
            steps {
               sh 'mvn clean package'
            }
        }
        stage('Sonar analasis') {
           steps {
               sh 'mvn sonar:sonar -Dsonar.host.url=http://3.144.173.142:9000 -Dsonar.login=de3618b88efbd2bb09b6bff3acaaffefd2103c64'
            }
        }
           stage('nexus') {
           steps {
            nexusArtifactUploader artifacts: [
            [artifactId: 'myweb',
            classifier: '',
            file: 'target/myweb-0.0.9.war',
            type: 'war']
            ],
            credentialsId: 'nexus',
            groupId: '',
            nexusUrl: '18.222.187.11:8081',
            nexusVersion: 'nexus3',
            protocol: 'http',
            repository: 'http://18.222.187.11:8081/repository/mani2-nexus/',
            version: '0.0.9'
            }
        }
              stage('tomcat deployment') {
            steps {
               sshagent(['tomcat']) {
              // some block
              sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@172.31.29.16:/opt/apache-tomcat-8.5.82/webapps/myweb-0.0.9.war'
                 }
              }
           }
        }
   }

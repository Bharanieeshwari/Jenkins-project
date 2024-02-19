pipeline {
  agent any
  tools {
      maven 'maven3.5'
  }
  stages {
      stage("Checkout") {
          steps {
              git url:'https://github.com/Bharanieeshwari/Jenkins-project.git', branch:'feature'
          }
      }
      stage('Build maven') {
          steps {
              sh 'mvn clean package'
          }
      }
      stage('Generate sonarqube-analysis') {
          steps {
              withSonarQubeEnv(installationName: 'sonarqube-8', credentialsId: 'Jenkins-sonar') {
                  sh 'mvn sonar:sonar'
              }
          }
      }
      stage('Deploy to Nexus') {
          steps {
              nexusArtifactUploader artifacts:[
                  [
                      artifactId: 'hiring',
                      classifier: '',
                      file: 'target/hiring.war',
                      type: 'war'
                  ]
              ],
              credentialsId: 'nexus-cred',
              groupId: 'in.javahome',
              nexusUrl: '44.221.161.192:8081',
              nexusVersion: 'nexus3',
              protocol: 'http',
              repository: 'test-nexus',
              version: 'pom.version'
          }
      }
      stage('Deploy to Tomcat') {
        steps {
          sshagent([credentials: ['tomcat-cred'], ignoreMissing: true]) {
           sh "scp -o StrictHostKeyChecking=no /var/lib/jenkins/workspace/Jenkins/target/hiring.war ubuntu@54.156.170.0:/opt/tomcat-9/webapps/"
        }
        }
    }
      
  }
}

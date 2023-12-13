pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "Maven3.9.5"
    }

    stages {
        stage('Checkout') {
            steps {
               checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/keyspaceits/javawebapp.git']]) 
            }
        }
        stage('Build') {
            steps {

                // Run Maven on a Unix agent.
                sh 'mvn clean install -f pom.xml'

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    slackSend channel: '#operations', message: 'Javaweb app build success', teamDomain: 'ksops', tokenCredentialId: 'slack', username: 'ksops'
                }
                
                 failure {
                    slackSend channel: '#operations', message: 'Javaweb app build has failed', teamDomain: 'ksops', tokenCredentialId: 'slack', username: 'ksops'
                }
            }
           
               
            
        }
        stage ('CodeQulity') {
            steps{
            withSonarQubeEnv('SonarQube') {
                 sh 'mvn clean install -f pom.xml sonar:sonar' 
                }
            }
        }

      stage ('NexusUpload') {
        steps {
            nexusArtifactUploader artifacts: [[artifactId: 'CounterWebApp', classifier: '', file: '/var/lib/jenkins/workspace/my-1st-jenkins-job/target/CounterWebApp.war', type: 'war']], credentialsId: 'NexusCred', groupId: 'com.mkyong', nexusUrl: '52.2.21.21:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0-SNAPSHOT'
        }
          
      }
        stage('Deploy to Prod') {
            steps {

                deploy adapters: [tomcat9(credentialsId: 'tomcat-server', path: '', url: 'http://54.164.45.31:8080/')], contextPath: null, war: '**/*.war'
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    slackSend channel: '#operations', message: 'Javaweb app deployment success', teamDomain: 'ksops', tokenCredentialId: 'slack', username: 'ksops'
                }
                
                 failure {
                    slackSend channel: '#operations', message: 'Javaweb app deployment has failed', teamDomain: 'ksops', tokenCredentialId: 'slack', username: 'ksops'
                }
            }
           
               
            
        }

    }
}

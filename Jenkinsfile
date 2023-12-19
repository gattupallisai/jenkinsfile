pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "maven 3.9.6"
    }

    stages {
        stage('CheckOut') {
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
                    slackSend channel: 'dev', message: 'javawebapp build sucess', teamDomain: 'devopsbysai', tokenCredentialId: 'slack_id', username: 'devopsbysai'
                }
                failure {
                    slackSend channel: 'dev', message: 'javawebapp build fail', teamDomain: 'devopsbysai', tokenCredentialId: 'slack_id', username: 'devopsbysai'
                }
            }
        }
        stage('deploy to prod') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'tomcat_server', path: '', url: 'http://54.86.178.31:8080')], contextPath: null, war: '**/*.war'

                
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    slackSend channel: 'dev', message: 'javawebapp deploy sucess', teamDomain: 'devopsbysai', tokenCredentialId: 'slack_id', username: 'devopsbysai'
                }
                failure {
                    slackSend channel: 'dev', message: 'javawebapp deploy fail', teamDomain: 'devopsbysai', tokenCredentialId: 'slack_id', username: 'devopsbysai'
                }
            }
        }
    }
}

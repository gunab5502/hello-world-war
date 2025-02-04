pipeline {
    agent { label 'builder'}
    environment {
        ARTIFACTORY_CREDENTIALS = credentials('jfrog_artifactory_credentials')
    }
        
    stages 
    {
        stage('checkout') {
            agent { label 'builder'}
            steps {
                sh 'ls'
                sh 'pwd'
            }
        }
         stage('build') {
             agent { label 'builder'}
            steps {
                sh 'mvn clean package'
            }
        }
         stage('publish') { 
             agent { label 'builder'}
            steps {
                sh 'mkdir -p ~/.m2'
                sh '''
                    echo "<settings>
                            <servers>
                                <server>
                                  <id>hello-world-war</id>
                                  <username>$ARTIFACTORY_CREDENTIALS_USR</username>
                                  <password>$ARTIFACTORY_CREDENTIALS_PSW</password>
                                </server>
                              </servers>
                            </settings>" > ~/.m2/settings.xml
                    '''
                sh 'cat ~/.m2/settings.xml'
                sh 'mvn clean deploy'
            }    
        }
        stage('deploy') {
           agent { label 'deployer'}
           steps {
                sh 'curl ifconfig.me'
                sh 'curl -L -u "${ARTIFACTORY_CREDENTIALS_USR}:${ARTIFACTORY_CREDENTIALS_PSW}" -O "http://35.175.199.164:8082/artifactory/jenkins-hello-world-libs-release/com/efsavage/hello-world-war/$BUILD_NUMBER/hello-world-war-$BUILD_NUMBER.war"'
                sh 'ls'
                sh 'sudo cp hello-world-war-$BUILD_NUMBER.war /opt/tomcat/apache-tomcat-10.1.34/webapps/'
                sh 'sudo bash /opt/tomcat/apache-tomcat-10.1.34/bin/shutdown.sh'
                sh 'sudo bash /opt/tomcat/apache-tomcat-10.1.34/bin/startup.sh'
           }
       }
    }   
}

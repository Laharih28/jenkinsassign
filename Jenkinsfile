pipeline {
    agent none  // No global agent since we'll specify per stage
    
    stages {
        stage("Checkout Code") {
            agent { label 'Slave' }
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/Laharih28/jenkinsassign.git',
                        credentialsId: 'github-token'
                    ]]
                ])
            }
        }
        
        stage("Parallel Stages") {
            parallel {
                stage("Build") {
                    agent { label 'Slave' }
                    steps {
                        sh '''
                        mvn clean install
                        '''
                    }
                }
                
                stage("Test") {
                    agent { label 'Slave1' }
                    steps {
                        sh '''
                        mvn test
                        '''
                    }
                }
            }
        }
        
        stage("Deploy To Artifactory") {
            agent { label 'Slave' }
            steps {
                configFileProvider([configFile(fileId: '64f5e694-7041-47ec-9a60-a2fe415dd855', variable: 'MAVEN_SETTINGS')]) {
                    sh '''
                    mvn deploy -s $MAVEN_SETTINGS
                    '''
                }
            }
        }
    }
}

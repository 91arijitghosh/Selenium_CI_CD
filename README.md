# Selenium_CI_CD

================================
Working parallel pipeline script
================================

pipeline {
    agent {
        label 'Hub'
	 }
    stages {
        stage('Run Tests') {
            parallel {
                stage('HubSetup') {
                    
                    steps {
                         checkout scmGit(branches: [[name: '*/CICD']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/91arijitghosh/Selenium_CI_CD.git']])
	                     
	                     sh '''
	                     cp /home/jenkins/selenium-server-4.7.2.jar /home/jenkins/workspace/Backup
	                     chmod +x selenium-server-4.7.2.jar
	                     java -jar selenium-server-4.7.2.jar hub &
						 sleep 30
						 
						 chmod +x $(pwd)/src/main/resources/chromedriver
                         mvn clean test
						
	                    '''
                    }
                }
                stage('NodeSetup') {
                    agent {
                        label 'Node'
                    }
                    steps {
                        sh '''
                        sleep 20
                        cp /home/jenkins/selenium-server-4.7.2.jar /home/jenkins/workspace/Backup
                        cp /home/jenkins/chromedriver /home/jenkins/workspace/Backup
	                    chmod +x selenium-server-4.7.2.jar
	                    chmod +x chromedriver
	  
	                    java -Dwebdriver.chrome.driver="chromedriver" -jar selenium-server-4.7.2.jar node --hub http://184.72.176.212:4444
	                    '''
                    }
                }
            }
        }
    }
}

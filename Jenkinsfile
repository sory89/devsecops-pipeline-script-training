pipeline {
    agent { label 'Jenkins-Agent' }
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    stages{
        stage("Cleanup Workspace"){
                steps {
                cleanWs()
                }
        }
      
        stage("Checkout from SCM"){
                steps {
                    git branch: 'main', credentialsId: 'github', url: 'https://github.com/sory89/devsecops-pipeline-script-training.git'
                }
        }      

        stage("Build Application"){
            steps {
                sh "mvn clean package"
            }

       }

       stage("Test Application"){
           steps {
                 sh "mvn test"
           }
       } 
       stage("SonarQube Analysis"){
           steps {
	           script {
		        withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') { 
                        sh "mvn sonar:sonar"
		        }
	           }	
           }
       }

       stage("Quality Gate"){
           steps {
               script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
                }	
            }

        }
	    
        stage('Push the change file to Git') {
            steps {
                script {
                    sh """
                       git config --global user.name "sory89"
                       git config --global user.email "sordiallo@gmail.com"
                       git add .
                       git commit -m "update the file deployment"

                    """
                    withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    sh "git push https://github.com/sory89/devsecops-pipeline-script-training.git main"
                    }
                }
            }
        }

        stage('Push the change file to Git') {
            sshagent(['ansible_connect']) {
		   sh 'ssh -o StrictHostKeyChecking=no vagrant@192.168.100.203 ls'
                }
            }
        }	
   }
}

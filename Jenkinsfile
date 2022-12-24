pipeline{
    
    agent any 
    
    tools{
        maven 'Maven3'
    }
    
    stages {
        
        stage('Git Checkout'){
            
            steps{
                
                script{
                    
                    git branch: 'main', url: 'https://github.com/AnjuS7/demo-counter-app.git'
                }
            }
        }
        stage('UNIT testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn test'
                }
            }
        }
        stage('Integration testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }
        stage('Maven build'){
            
            steps{
                
                script{
                    
                    sh 'mvn clean install'
                }
            }
        }
        stage('Static code analysis'){
            
            steps{
                
                script{
                    
                    withSonarQubeEnv('sonarqube') {
                        
                        sh 'mvn clean package sonar:sonar'
                    
                   }
                }
                    
                }
            }
            stage('Quality Gate Status'){
                
                steps{
                    script{ 
                   timeout(time: 1, unit: 'HOURS') {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }
                }
            }
        }
         stage('Upload jar file to Nexus')
    {
        steps{
            script{
                nexusArtifactUploader artifacts: [[artifactId: 'springboot', classifier: '', file: 'target/Uber.jar', type: 'jar']], credentialsId: 'nexus_auth', groupId: 'com.example', nexusUrl: '3.88.171.130:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'App_release', version: '1.0.0'
            
            }
        }
        
    }
        
}
   
}

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
                
               
                    
                    withSonarQubeEnv('sonarqube') {
                        
                        sh 'mvn clean package sonar:sonar'
                    
                   }
                    
                }
            }
            stage('Quality Gate Status'){
                
                steps{
                    
                   timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
                }
            }
        }
        
}

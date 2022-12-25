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
                def readpomversion = readMavenPom file: 'pom.xml'
                def nexusRepo = readpomversion.version.endsWith("SNAPSHOT")?"App_snapshot":"App_release"
                nexusArtifactUploader artifacts: [[artifactId: 'springboot', classifier: '', file: 'target/Uber.jar', type: 'jar']], 
                    credentialsId: 'nexus_auth', groupId: 'com.example', nexusUrl: '107.21.136.40:8081', nexusVersion: 'nexus3',
                    protocol: 'http', repository: nexusRepo, version: "${readpomversion.version}"
            
            }
        }
        
    }
        stage('Docker image build')
        {
            steps{
                script{
                sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                sh 'docker image tag $JOB_NAME:v1.$BUILD_ID anju/$JOB_NAME:v1.$BUILD_ID'
                sh 'docker image tag $JOB_NAME:v1.$BUILD_ID anju/$JOB_NAME:latest'    
                }
            }
            
        }
     stage('Pushing to ECR') {
     steps{  
         script {
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 902079157234.dkr.ecr.us-east-1.amazonaws.com'
                sh 'docker push 902079157234.dkr.ecr.us-east-1.amazonaws.com/demo_repo:latest'
         }
        }
      }
   
         // Stopping Docker containers for cleaner Docker run
     stage('stop previous containers') {
         steps {
            sh 'docker ps -f name=myContainer -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=myContainer -q | xargs -r docker container rm'
         }
       }
      
    stage('Docker Run') {
     steps{
         script {
                sh 'docker run -d -p 8096:9090 --rm --name myContainer 902079157234.dkr.ecr.us-east-1.amazonaws.com/demo_repo:latest'
            }
      }
    }   
}
   
}

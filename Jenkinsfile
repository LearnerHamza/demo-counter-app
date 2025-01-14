pipeline{
    
    agent any 
    
    stages {
        
        stage('Git Checkout'){
            
            steps{
                
                script{
                    
                    git branch: 'main', url: 'https://github.com/LearnerHamza/demo-counter-app.git'
                    
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
                    
                    withSonarQubeEnv(credentialsId: 'SonarCred') {
    // some block                        
                        sh 'mvn clean package sonar:sonar'
                    }
                   }
                    
                }
            }
         stage('Quality Gate Status'){
            
            steps{
                
                script{
                    
                    waitForQualityGate abortPipeline: false, credentialsId: 'SonarCred'
                }
            }
        }
        stage('upload war file to nexus'){
            steps{
                script{
                    def readpomVersion = readMavenPom file: 'pom.xml'
                    def nexusrepo = readpomVersion.version.endsWith("SNAPSHOT") ? "demoapp-snapshot" : "demoapp-release"
                    
                nexusArtifactUploader artifacts: [
                    [artifactId: 'springboot', 
                     classifier: '', 
                     file: 'target/Uber.jar', 
                     type: 'jar'
                    ]
                ], 
                    credentialsId: 'Nexus-Cred',
                    groupId: 'com.example', 
                    nexusUrl: '13.72.72.88:8081', 
                    nexusVersion: 'nexus3',
                    protocol: 'http', 
                    repository: nexusrepo, 
                    version: "${readpomVersion.version}"
                }
            }
        }
        stage("Docker Image Build"){
            steps{
                script{

                    sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                    sh 'docker image tag $JOB_NAME:v1.$BUILD_ID zafarhamza/$JOB_NAME:v1.$BUILD_ID'
                    sh 'docker image tag $JOB_NAME:v1.$BUILD_ID zafarhamza/$JOB_NAME:latest'
                }
            }
        }
        stage("Docker image to the dockerHub"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'Docker_Hub', variable: 'Docker_Hub_Cred')]) 
                    {
                        sh 'docker login -u zafarhamza -p ${Docker_Hub_Cred}'
                        sh 'docker image push zafarhamza/$JOB_NAME:v1.$BUILD_ID'
                        sh 'docker image push zafarhamza/$JOB_NAME:latest'

                     }
                }
            }
        }
        stage("EKS Connect"){
            steps{
                script{
                    echo "test"      
                }
            }
        }
    }  
}

pipeline {
    agent any
    
    tools {
        jdk 'jdk11'
        maven 'maven3'
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout ') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/devopssrinu464/SpringBoot-WebApplication.git'
            }
        }
        
        stage('Code Compile') {
            steps {
                    sh "mvn compile"
            }
        }
        
        stage('Run Test Cases') {
            steps {
                    sh "mvn test"
            }
        }
        
        stage('Sonarqube Analysis') {
            steps {
                    withSonarQubeEnv('sonar-server') {
                        sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Java-WebApp \
                        -Dsonar.java.binaries=. \
                        -Dsonar.projectKey=Java-WebApp '''
    
                }
            }
        }
        
        //stage('OWASP Dependency Check') {
          //  steps {
            //       dependencyCheck additionalArguments: '--scan ./   ', odcInstallation: 'DP'
              //     dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            //}
        //}
        
        stage('Maven Build') {
            steps {
                    sh "mvn clean package"
            }
        }
        
        stage('Docker Build & Push') {
            steps {
                   script {
                       withDockerRegistry(credentialsId: 'docker-cred') {
                            sh "docker build -t webapp ."
                            sh "docker tag webapp devopssep/webapp:latest"
                            sh "docker push devopssep/webapp:latest "
                        }
                   } 
            }
        }
        
        stage('Docker Image scan') {
            steps {
                    sh "trivy image devopssep/webapp:latest"
            }
        }
          stage('Deploy to Docker') {
            steps {
               script{
                   withDockerRegistry(credentialsId: 'docker-cred') {
                    sh "docker run -d --name webapp -p 8081:8080 devopssep/webapp:latest "
                   }
               }
            }
          }
    }
}

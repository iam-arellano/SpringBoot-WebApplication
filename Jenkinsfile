pipeline {
    agent any
    
    tools {
        jdk 'java17'
        maven 'maven3'
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout ') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/iam-arellano/SpringBoot-WebApplication'
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
        
          stage("SonarQube Analysis"){
           steps {
	           script {
		        withSonarQubeEnv(credentialsId: 'sonarqube_access') { 
                        sh "mvn sonar:sonar"
		        }
	           }	
           }
       }
        
        // stage('OWASP Dependency Check') {
        //     steps {
        //            dependencyCheck additionalArguments: '--scan ./   ', odcInstallation: 'DP'
        //            dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
        //     }
        // }
        
        stage('Maven Build') {
            steps {
                    sh "mvn clean package"
            }
        }
        
        stage('Docker Build & Push') {
            steps {
                   script {
                 
                            withDockerRegistry(credentialsId: 'jenkins-docker-credentials', toolName: 'docker-from-manage-tools')  {
                            sh "docker build -t springboot-webapp ."
                            sh "docker tag springboot-webapp raemondarellano/springboot-webapp:latest"
                            sh "docker push raemondarellano/springboot-webapp:latest "
                        }
                   } 
            }
        }
        
        // stage('Docker Image scan') {
        //     steps {
        //             sh "trivy image raemondarellano/springboot-webapp::latest "
        //     }
        // }
            stage("Trivy Scan") {
           steps {
               script {
	            sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image raemondarellano/springboot-webapp:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
               }
           }
       }
        
    }
}

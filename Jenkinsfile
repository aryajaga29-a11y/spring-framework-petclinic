pipeline {
    agent any
    tools {
        // Define the Maven and JDK tools for Windows
        maven 'maven'
        jdk 'OpenJDK-17'
    }
    
    environment {
        // Define the SonarQube Scanner path for Windows
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/aryajaga29-a11y/spring-framework-petclinic'
            }
        }

        // stage('SCA with OWASP Dependency Check') {
        //     steps {
        //         // Run Dependency-Check with a custom output directory and HTML format report
        //         dependencyCheck additionalArguments: '--format HTML --out ./dependency-check-report', odcInstallation: 'DP-Check'
        //     }
        // }

        stage('Maven Build') {
            steps {
                powershell 'mvn clean install -DskipTests'  // Use PowerShell on Windows
            }
        }

        stage('SAST with SonarQube') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    powershell 'mvn sonar:sonar'  // Use PowerShell for SonarQube scan on Windows
                }
            }
        }

        stage('Docker Build Image') {
            steps {
                powershell 'docker build -t https://hub.docker.com/repositories/jagadeesh078 .'  // Use PowerShell for Docker build
            }
        }

        stage('Docker Push Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker') {
                        powershell 'docker push https://hub.docker.com/repositories/jagadeesh078'  // Use PowerShell for Docker push
                    }
                }
            }
        }

        stage('Trivy Scan on Docker Image') {
            steps {
                powershell 'trivy image https://hub.docker.com/repositories/jagadeesh078/new:latest'  // Use PowerShell for Trivy scan
            }
        }

        stage('Deploying Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker') {
                        powershell 'docker run -d --name new2 -p 8081:8080 https://hub.docker.com/repositories/jagadeesh078:latest'  // Use PowerShell for Docker run
                    }
                }
            }
        }
    }
}

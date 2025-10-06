pipeline {
    agent any
    tools {
        maven 'maven'
        jdk 'OpenJDK-17'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        IMAGE_NAME = 'jagadeesh078/spring-framework-petclinic'  // Docker image name
        IMAGE_TAG = 'latest'  // Docker image tag
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/aryajaga29-a11y/spring-framework-petclinic'
            }
        }

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

        // stage('Docker Build Image') {
        //     steps {
        //         powershell "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."  // Use PowerShell for Docker build
        //     }
        // }

        stage('Docker build and Push Image') {
            steps {
                script {
                    // Use Jenkins credentials to login to Docker
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        // Set the environment variables for docker login
                        withEnv(["DOCKER_USERNAME=${DOCKER_USERNAME}", "DOCKER_PASSWORD=${DOCKER_PASSWORD}"]) {
                            // Run the docker login non-interactively
                            bat """
                    echo %DOCKER_PASSWORD% | docker login -u %DOCKER_USERNAME% --password-stdin
                    docker build -t %IMAGE_NAME%:%IMAGE_TAG% .
                    docker push %IMAGE_NAME%:%IMAGE_TAG%
                """
                        }
                    }
                }
            }
        }

        stage('Trivy Scan on Docker Image') {
            steps {
                powershell "trivy image ${IMAGE_NAME}:${IMAGE_TAG}"  // Use PowerShell for Trivy scan
            }
        }

        stage('Deploying Docker Image') {
            steps {
                script {
                    powershell "docker run -d --name new2 -p 8081:8080 ${IMAGE_NAME}:${IMAGE_TAG}"  // Use PowerShell for Docker run
                }
            }
        }
    }
}

pipeline {
    agent any
    tools {
        nodejs 'node21'
    }
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('git checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/surendraaaaa/3-Tier-Full-Stack-CICD.git'
            }
        }
        stage('install dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('unit tests') {
            steps {
                sh "npm test"
            }
        }
        stage('Trivy FS scan') {
            steps {
                sh "trivy fs --format table -o fs-report.html ."
            }
        }
        stage('Sonar Scan') {
            steps {
                withSonarQubeEnv('sonar') {
                        sh " $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=devops -Dsonar.projectName=devops"
                     }
             }
        }
        
        stage('Docker build and tag') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerhub-ashu') {
                        sh "docker build -t ashuk480/3-tier:latest ."
                   }
                }
            }
        }
        
        stage('Trivy docker image scan') {
            steps {
                sh "trivy image --format table -o fs-report.html ashuk480/3-tier:latest"
            }
        }
        
        stage('Docker push image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerhub-ashu') {
                        sh "docker push ashuk480/3-tier:latest"
                   }
                }
            }
        }
        
        stage('Docker Deloyment') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerhub-ashu') {
                        sh "docker run -d -p 3000:3000 ashuk480/3-tier:latest"
                   }
                }
            }
        }
    }
    
    
}


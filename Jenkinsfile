pipeline {
    agent any

    environment {
        registry = "211223789150.dkr.ecr.us-east-1.amazonaws.com/my-docker-repo"
        NEXUS_VERSION = "3.30.0-01"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "http://192.168.0.106:8085"
        NEXUS_REPOSITORY = "MavenSpringBootApp"
        NEXUS_CREDENTIAL_ID = "India@#$1234"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/akannan1087/docker-spring-boot']])
            }
        }
        
        stage ("Build JAR") {
            steps {
                sh "mvn clean install"
            }
        }
        
        stage ("Build Image") {
            steps {
                script {
                    docker.build registry
                }
            }
        }
        
        stage ("Push to ECR") {
            steps {
                script {
                    sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 211223789150.dkr.ecr.us-east-1.amazonaws.com"
                    sh "docker push 211223789150.dkr.ecr.us-east-1.amazonaws.com/my-docker-repo:latest"
                    
                }
            }
        }
        
        stage ("Helm package") {
            steps {
                    sh "helm package springboot"
                }
            }
                
        stage ("Helm install") {
            steps {
                    sh "helm upgrade myrelease-21 springboot-0.1.0.tgz"
                }
            }
    }
}

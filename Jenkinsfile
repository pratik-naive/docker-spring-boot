pipeline {
    agent any

    environment {
        registry = "211223789150.dkr.ecr.us-east-1.amazonaws.com/my-docker-repo"
        NEXUS_VERSION = "3.30.0-01"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "http://192.168.0.106:8085"
        NEXUS_REPOSITORY = "MavenSpringBootApp"
        NEXUS_CREDENTIAL_ID = "nexus_Cred"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/akannan1087/docker-spring-boot']])
            }
        }
        
        stage("Build JAR") {
            steps {
                sh "mvn clean install"
            }
        }
        
        stage("Upload to Nexus") {  // Added stage for uploading JAR to Nexus
            steps {
                script {
                    nexusArtifactUploader artifacts: [
                        [
                            artifactId: 'docker-spring-boot',
                            classifier: '',
                            file: 'target/docker-spring-boot-1.0.jar',  // Ensure this matches your artifact name
                            groupId: 'com.devops.coach',
                            packaging: 'jar',
                            version: '1.0'
                        ]
                    ],
                    credentialsId: env.NEXUS_CREDENTIAL_ID,
                    nexusUrl: env.NEXUS_URL,
                    nexusVersion: 'nexus3',
                    protocol: env.NEXUS_PROTOCOL,
                    repository: env.NEXUS_REPOSITORY
                }
            }
        }
        
        stage("Build Image") {
            steps {
                script {
                    docker.build registry
                }
            }
        }
        
        stage("Push to ECR") {
            steps {
                script {
                    sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${registry}"
                    sh "docker push ${registry}:latest"
                }
            }
        }
        
        stage("Helm Package") {
            steps {
                sh "helm package springboot"
            }
        }
        
        stage("Helm Install") {
            steps {
                sh "helm upgrade --install myrelease-21 springboot-0.1.0.tgz"
            }
        }
    }
}

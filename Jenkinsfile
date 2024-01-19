pipeline {
    agent any

    tools {
        jdk 'jdk11'
        maven 'Maven3'
    }
    
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/welcomeabhay/ekart-java.git'
            }
        }
        
        stage('BUILD') {
            steps {
                script {
                    sh '''
                        mvn clean package -DskipTests=true
                    '''
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectName=ekart-pipeline \
                        -Dsonar.java.binaries=. \
                        -Dsonar.projectKey=ekart-pipeline \
                        -Dsonar.sources=src
                    '''
                }
            }
        }
        
        stage('Nexus') {
            steps {
                script {
                    nexusArtifactUploader(
                        credentialsId: 'nexus',
                        groupId: 'com.reljicd',
                        nexusUrl: '3.143.210.232:8081',
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        repository: 'java-pipeline-app',
                        version: '1.0-SNAPSHOT',
                        artifacts: [
                            [
                                artifactId: 'shopping-cart',
                                classifier: '',
                                file: "${workspace}/target/shopping-cart-0.0.1-SNAPSHOT.jar",
                                type: 'jar'
                            ]
                            // Add more artifacts if needed
                        ]
                    )
                }
            }
        }
    }
}

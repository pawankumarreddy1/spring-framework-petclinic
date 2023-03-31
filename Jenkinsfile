pipeline {
    agent any
    tools {
        maven "Maven"
    }

    environment {
        NEXUS_CREDENTIALS = sh(script: 'aws secretsmanager get-secret-value --secret-id nexus-credentials --query SecretString --output text', returnStdout: true).trim()
        BUILD_NUMBER = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Build') {
            steps {
                sh script: "mvn clean package -Dbuild.number=${BUILD_NUMBER}"
            }
        }

        stage('Upload to Nexus') {
            steps {
                nexusArtifactUploader nexusInstanceId: 'nexus3', nexusUrl: 'http://nexus-url.com', groupId: 'com.example', version: '1.0.${BUILD_NUMBER}', repository: 'maven-repo', credentialsId: 'nexus-credentials', artifacts: [
                    [
                        artifactId: 'spring-boot-app',
                        type: 'war',
                        classifier: '',
                        file: 'target/spring-boot-app.war'
                    ]
                ]
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                sh 'curl -O http://nexus-url.com/repository/maven-repo/com/example/spring-boot-app/1.0.${BUILD_NUMBER}/spring-boot-app-1.0.${BUILD_NUMBER}.war'
                sh 'scp spring-boot-app-1.0.${BUILD_NUMBER}.war user@172.31.80.162:/opt/tomcat/webapps'
            }
        }
    }
}





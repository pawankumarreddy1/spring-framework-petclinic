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
                nexusArtifactUploader nexusInstanceId: 'nexus3', nexusUrl: '172.31.30.144:8081', groupId: 'org.springframework.samples', version: '1.0.${BUILD_NUMBER}', repository: 'jenkins-project', credentialsId: 'nexus-credentials', artifacts: [
                    [
                        artifactId: 'spring-framework-petclinic',
                        type: 'war',
                        classifier: '',
                        file: '/var/lib/jenkins/workspace/pavan-project/target/petclinic-${BUILD_NUMBER}.war'
                    ]
                ]
            }
        }

//         stage('Deploy to Tomcat') {
//             steps {
//                 sh 'curl -O com/example/spring-boot-app/1.0.${BUILD_NUMBER}/spring-boot-app-1.0.${BUILD_NUMBER}.war'
//                 sh 'scp spring-boot-app-1.0.${BUILD_NUMBER}.war user@172.31.80.162:/opt/tomcat/webapps'
//             }
//         }
    }
}





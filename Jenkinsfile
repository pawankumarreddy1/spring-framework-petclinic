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
        stage('Upload files to Nexus') {
            steps {
                nexusArtifactUploader artifacts: [
                    [
                        artifactId: 'spring-framework-petclinic',
                        classifier: '',
                        file: "/var/lib/jenkins/workspace/pavan-project/target/petclinic.war",
                        type: 'war'
                    ]
                ],
                credentialsId: 'nexus3',
                groupId: 'org.springframework.samples',
                nexusUrl: '172.31.30.144:8081',
                nexusVersion: 'nexus3',
                protocol: 'http',
                repository: 'jenkins-project',
                version: '1.0.${BUILD_NUMBER}'
            }
        }
        stage('Deploy to Tomcat server') {
            steps {
                sh "echo '${NEXUS_CREDENTIALS}' > nexus-credentials.properties"
                sh 'curl -u $(cat nexus-credentials.properties | jq -r ".username"):$(cat nexus-credentials.properties | jq -r ".password") -O http://54.89.231.225:8081/repository/jenkins-project/org/springframework/samples/spring-framework-petclinic/5.3.22/spring-framework-petclinic-5.3.22.war'
                sh "mv spring-framework-petclinic-5.3.22.war petclinic-${BUILD_NUMBER}.war"
                sh 'scp petclinic-${BUILD_NUMBER}.war root@172.31.80.162:/opt/tomcat/webapps'
            }
        }
    }
} 





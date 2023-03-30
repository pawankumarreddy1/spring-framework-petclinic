pipeline {
    agent any
    tools {
        maven "Maven"
    }
    environment {
        NEXUS_URL = "http://172.31.30.144:8081"
        NEXUS_VERSION = "nexus3"
        NEXUS_CRED_SECRET_NAME = "my-nexus-secret"
        TOMCAT_HOST = "172.31.80.162"
    }
    stages {
        stage('Build') {
            steps {
                sh script: 'mvn clean package'
            }
        }
        stage('Upload files to Nexus') {
            steps {
                nexusArtifactUploader artifacts: [
                    [
                        artifactId: 'spring-framework-petclinic',
                        classifier: '',
                        file: '/var/lib/jenkins/workspace/pavan-project/target/petclinic.war',
                        type: 'war'
                    ]
                ],
                credentialsId: 'nexus3',
                groupId: 'org.springframework.samples',
                nexusUrl: env.NEXUS_URL,
                nexusVersion: env.NEXUS_VERSION,
                protocol: 'http',
                repository: 'jenkins-project',
                version: '5.3.22'

            }
        }
        stage('Deploy to Tomcat Server') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: env.NEXUS_CRED_SECRET_NAME, secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                    sh 'aws secretsmanager get-secret-value --secret-id $NEXUS_CRED_SECRET_NAME --query SecretString --output text > nexus_creds.properties'
                    withEnv(['USER=admin', "PASSWORD=${aws ssm get-parameters --names 'nexus-admin-password' --with-decryption --query 'Parameters[0].Value' --output text}"]) {
                        sh 'wget --user=$USER --password=$PASSWORD $NEXUS_URL/repository/jenkins-project/org/springframework/samples/spring-framework-petclinic/5.3.22/spring-framework-petclinic-5.3.22.war'
                    }
                }
                sh 'mv spring-framework-petclinic-5.3.22.war petclinic.war'
                sh "scp petclinic.war $TOMCAT_HOST:/opt/tomcat/webapps"
            }
        }
    }
}

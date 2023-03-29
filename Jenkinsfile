pipeline {
    agent any
    tools{
        maven "Maven"
    }
    
    stages {
        stage('Build') {
            steps {
                sh script: 'mvn clean package'
            }
        }
        stage('uplosd files to nexus') {
            steps {
                nexusArtifactUploader artifacts: [
                    [
                        artifactId: 'spring-framework-petclinic', 
                        classifier: '', 
                        file: '/target/petclinic.war', 
                        type: 'war'
                    ]
                ], 
                credentialsId: 'nexus3', 
                groupId: 'org.springframework.samples', 
                nexusUrl: '172.31.30.144:8081', 
                nexusVersion: 'nexus3', 
                protocol: 'http', 
                repository: 'jenkins-pavan', 
                version: '5.3.22'
                
            }
        }
    }
}

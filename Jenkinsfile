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
                        file: '/var/lib/jenkins/workspace/pavan-project/target/petclinic.war ', 
                        type: 'war'
                    ]
                ], 
                credentialsId: 'nexus3', 
                groupId: 'org.springframework.samples', 
                nexusUrl: '172.31.30.144:8081', 
                nexusVersion: 'nexus3', 
                protocol: 'http', 
                repository: 'pavan-nexus', 
                version: '5.3.22'
                
            }
            
        }
        stage('deploy to the tomcat server') {
            steps {
                sh 'wget --user=admin --password=pavan123 http://3.88.29.196:8081/repository/pavan-nexus/http://3.88.29.196:8081/repository/pavan-nexus/org/springframework/samples/spring-framework-petclinic/5.3.22/spring-framework-petclinic-5.3.22.war'
                sh 'mv spring-framework-petclinic-5.3.22.war petclinic.war'
                sh 'scp petclinic.war root@172.31.80.162:/opt/tomcat/webapps'
            }
        }
    }
}

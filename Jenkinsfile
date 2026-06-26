node{
    def Mavenhome = tool name: 'maven-3.9.0', type: 'maven'
    stage("git clone") {
        git branch: 'development', credentialsId: 'gihub-cred', url: 'https://github.com/reshma229/student-reg-webapp.git'
    }
    stage("maven build") {
        withCredentials([string(credentialsId: 'Sonartoken', variable: 'Sonartoken')]) {
        sh "${Mavenhome}/bin/mvn clean verify -Dsonar.token=${Sonartoken}"
}
    }
    stage("maven deploy") {
        sh "${Mavenhome}/bin/mvn clean deploy"
    }
    stage("stop  tomcat process") {
    sshagent(['tomcat_server']) {
     sh"""
     echo stopping tomcat process
     ssh -o StrictHostkeyChecking=no ec2-user@172.31.72.241 sudo systemctl stop tomcat
     sleep 10
     """
    }
   }
    
    stage("deploy war to tomcat")
    sshagent(['tomcat_server']) {
    sh "scp -o StrictHostkeyChecking=no target/student-reg-webapp.war ec2-user@172.31.72.241:/opt/tomcat/webapps/"
}
stage("starting  tomcat process") {
    sshagent(['tomcat_server']) {
    sh"""
    echo starting tomcat process
    ssh -o StrictHostkeyChecking=no ec2-user@172.31.72.241 sudo systemctl start tomcat
    """
    }
}
    
    
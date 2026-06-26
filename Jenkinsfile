node{
    def Mavenhome = tool name: 'maven-3.9.0', type: 'maven'
    try {
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
    
    } catch (err) {
        currentBuild.result = 'FAILURE'
        
    } finally {
        def build status = currentBuild.result ?: 'SUCCESS'
        sendEmail(
        subject: "${env.JOB_NAME} -${env.BUILD_NUMBER} - ${build status}", 
        body:"""Hi team
        please check the logs at ${env.BUILD_URL}""", 'shahabsk135@gmail.com')
    } 
}
def sendEmail(String subject, String body, String recipient) {
    emailext (
        subject: "${env.JOB_NAME} - Build # ${env.BUILD_NUMBER} - ${buildStatus}",
        body: """<p>Build URL: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>""",
        to: 'shahabsk135@gmail.com'
    )
}

        
        
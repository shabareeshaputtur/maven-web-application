node
{
    
echo "the job name is: ${env.JOB_NAME}"
    
properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])

def mavenHome = tool name: "maven3.8.6"
stage('CheckoutCode'){
git branch: 'development', credentialsId: '041498a0-bdb3-4675-822a-3f3da8f0157b', url: 'https://github.com/shabareeshaputtur/maven-web-application.git'
}
stage('Build'){
sh "${mavenHome}/bin/mvn clean package"
}
stage("ExecuteSonarQube"){
sh "${mavenHome}/bin/mvn sonar:sonar"
}
stage("UploadArtifactsintoNexus"){
sh "${mavenHome}/bin/mvn deploy"
}
stage("DeployAppIntoTomcatServer"){
sshagent(['4cce997b-1a50-4263-a745-73f0dbf4836a']){
    sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@65.0.85.191:/opt/apache-tomcat-9.0.64/webapps"
}
}
    
}

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
    
}//node closing

node {
    try {
        notifyBuild('STARTED')

        stage('Prepare code') {
            echo 'do checkout stuff'
        }

        stage('Testing') {
            echo 'Testing'
            echo 'Testing - publish coverage results'
        }

        stage('Staging') {
            echo 'Deploy Stage'
        }

        stage('Deploy') {
            echo 'Deploy - Backend'
            echo 'Deploy - Frontend'
        }

  } catch (e) {
    // If there was an exception thrown, the build failed
    currentBuild.result = "FAILED"
    throw e
  } finally {
    // Success or failure, always send notifications
    notifyBuild(currentBuild.result)
  }
}

def notifyBuild(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}

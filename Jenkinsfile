node
{
 def notifyBuild(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESS'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESS') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}
 def mavenHome = tool name: "maven3.8.4"

 stage('chechout')
  {
  git branch: 'development', credentialsId: '921c7cde-2f31-4345-8cfe-0b3955bce7c6', url: 'https://github.com/hiteshsinghnayak/maven-web-application.git'
  }
 stage('Build')
  {
  sh "${mavenHome}/bin/mvn clean package"
  }
  stage('executesonarqube')
  {
  sh "${mavenHome}/bin/mvn clean sonar:sonar"
  }
  stage('uploadartifactintonexus')
  {
  sh "${mavenHome}/bin/mvn clean deploy"
  }
  stage('deployintomcat')
  {
   sshagent(['fcb48f9d-422b-4f02-89c0-ef61a0bb6618']) {
    sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@52.66.246.178:/opt/apache-tomcat-9.0.56/webapps/"
  }  
  }
  stage('emailnotify')
 {
 emailext body: 'Done with the build', subject: 'Done with the build', to: 'hitesh.nayak10@gmail.com'
 }
 
 notifyBuild(currentBuild.result)
}

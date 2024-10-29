import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=5454bad1-6322-4a81-839b-67c6923b99a6',
        'AZURE_TENANT_ID=b39c260a-3b5a-4fdd-a717-f3cb39887ae8']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      sh 'mvn clean package'
    }
  
    stage('deploy') {
      def resourceGroup = 'jenkins-get-started-rg'
      def webAppName = 'xpk403'
      // login Azure
      withCredentials([usernamePassword(credentialsId: 'AzureServicePrincipal', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
       sh '''
          az login --service-principal -u 7cdfe7b6-72b9-4246-9959-e7eec5ab6de9 -p TQR8Q~00CqprsUa~.eA8SQsn5jjel6j.d7QHKaoL --tenant b39c260a-3b5a-4fdd-a717-f3cb39887ae8
          az account set -s 5454bad1-6322-4a81-839b-67c6923b99a6
        '''
      }
      // get publish settings
      def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g jenkins-get-started-rg -n xpk403", returnStdout: true
      def ftpProfile = getFtpPublishProfile pubProfilesJson
      // upload package
     sh "curl -T target/calculator-1.0.war ftps://waws-prod-blu-229.ftp.azurewebsites.windows.net/site/wwwroot/ROOT.war -u xpk403\\\$xpk403:pvppYAToxi62lcvStHD4uD9e6bYQXMJ1hClSnxtWgpmmYFlDoNtzYqoG82G7"

      // log out
      sh 'az logout'
    }
  }
}

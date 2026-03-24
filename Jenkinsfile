import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=64f78ab2-3af3-4721-8682-eefd8821d34c',
        'AZURE_TENANT_ID=0236eal1-0679-4072-9403-5c5ded97bd6f']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      sh 'mvn clean package'
    }
  
    stage('deploy') {
    withCredentials([usernamePassword(credentialsId: 'AzurePublishProfile', usernameVariable: 'FTP_USER', passwordVariable: 'FTP_PASS')]) {
        sh """
        curl -T target/calculator-1.0.war \
        ftp://waws-prod-ch1-075.ftp.azurewebsites.windows.net/site/wwwroot/webapps/ROOT.war \
        -u "$FTP_USER:$FTP_PASS"
        """
    }
}
      // get publish settings
      def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g $resourceGroup -n $webAppName", returnStdout: true
      def ftpProfile = getFtpPublishProfile pubProfilesJson
      // upload package
      sh "curl -T target/calculator-1.0.war $ftpProfile.url/webapps/ROOT.war -u '$ftpProfile.username:$ftpProfile.password'"
      // log out
      sh 'az logout'
    }
  }
}

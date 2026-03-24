import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
    stage('init') {
        checkout scm
    }

    stage('build') {
        sh 'mvn clean package'
    }

    stage('deploy') {
        withCredentials([usernamePassword(
            credentialsId: 'AzurePublishProfile',
            usernameVariable: 'FTP_USER',
            passwordVariable: 'FTP_PASS'
        )]) {
            sh '''
            curl -T target/calculator-1.0.war \
            ftp://waws-prod-ch1-075.ftp.azurewebsites.windows.net/site/wwwroot/webapps/ROOT.war \
            -u "$FTP_USER:$FTP_PASS"
            '''
        }
    }
}

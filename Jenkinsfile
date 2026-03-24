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
    withEnv([
        'AZURE_TENANT_ID=你的tenant-id',
        'AZURE_SUBSCRIPTION_ID=你的subscription-id'
    ]) {
        withCredentials([usernamePassword(
            credentialsId: 'AzureServicePrincipal',
            usernameVariable: 'AZURE_CLIENT_ID',
            passwordVariable: 'AZURE_CLIENT_SECRET'
        )]) {
            sh '''
                az login --service-principal \
                  -u "$AZURE_CLIENT_ID" \
                  -p "$AZURE_CLIENT_SECRET" \
                  -t "$AZURE_TENANT_ID"

                az account set --subscription "$AZURE_SUBSCRIPTION_ID"

                az webapp deploy \
                  --resource-group jenkins-get-started-rg \
                  --name <你的webapp名字> \
                  --src-path target/calculator-1.0.war \
                  --type war
            '''
        }
    }
}

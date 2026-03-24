
node {
    stage('init') {
        checkout scm
    }

    stage('build') {
        sh 'mvn clean package'
    }

    stage('deploy') {
    withEnv([
        'AZURE_TENANT_ID=0236ea11-0679-4072-9403-5c5ded97bd6f',
        'AZURE_SUBSCRIPTION_ID=64f78ab2-3af3-4721-8682-eefd8821d34c'
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
                  --name shengshi-java-Jenkins \
                  --src-path target/calculator-1.0.war \
                  --type war
            '''
        }
    }
}

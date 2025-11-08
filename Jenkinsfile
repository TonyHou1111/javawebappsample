pipeline {
  agent any

  environment {
    AZURE_SUBSCRIPTION_ID = '21ecd3ac-e93a-4949-b7a8-ed330e37b660'
    AZURE_TENANT_ID       = '1f710310-6981-437a-98a4-f51411fe7da8'
    RESOURCE_GROUP        = 'jenkins-rg-asia'
    WEBAPP_NAME           = 'jx55731393'
  }

  stages {
    stage('Build') {
      steps {
        sh 'mvn -v'
        sh 'mvn clean package -DskipTests'
        sh 'ls -lh target || true'
      }
    }

    stage('Deploy to Azure Web App') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'AzureServicePrincipal',
          usernameVariable: 'AZURE_CLIENT_ID',
          passwordVariable: 'AZURE_CLIENT_SECRET'
        )]) {
          sh '''
            set -e
            echo "Azure CLI version:" && az version

            az login --service-principal \
              -u "$AZURE_CLIENT_ID" \
              -p "$AZURE_CLIENT_SECRET" \
              --tenant "$AZURE_TENANT_ID"

            az account set --subscription "$AZURE_SUBSCRIPTION_ID"

            az webapp deploy \
              --resource-group "$RESOURCE_GROUP" \
              --name "$WEBAPP_NAME" \
              --src-path target/calculator-1.0.war \
              --type war

            echo "✅ 部署成功： https://$WEBAPP_NAME.azurewebsites.net"
          '''
        }
      }
    }
  }
}

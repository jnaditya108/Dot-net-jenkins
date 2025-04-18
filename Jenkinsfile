pipeline {
    agent any

    environment {
        AZURE_SUBSCRIPTION_ID = '054f2ec0-512e-4743-b678-f72b4ac7435f'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/jnaditya108/Dot-net-jenkins.git'
            }
        }

        stage('Restore Dependencies') {
            steps {
                bat 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                bat 'dotnet build --configuration Release'
            }
        }

        stage('Publish') {
            steps {
                bat 'dotnet publish -c Release -o published'
            }
        }

        stage('Package App') {
            steps {
                powershell 'Compress-Archive -Path published\\* -DestinationPath app.zip -Force'
            }
        }

        stage('Deploy to Azure') {
            steps {
                withCredentials([azureServicePrincipal(
                    credentialsId: 'Azure-service-principle',
                    subscriptionIdVariable: 'AZURE_SUBSCRIPTION_ID',
                    clientIdVariable: 'AZURE_CLIENT_ID',
                    clientSecretVariable: 'AZURE_CLIENT_SECRET',
                    tenantIdVariable: 'AZURE_TENANT_ID'
                )]) {
                    bat '''
                    az login --service-principal ^
                        --username "%AZURE_CLIENT_ID%" ^
                        --password "%AZURE_CLIENT_SECRET%" ^
                        --tenant "%AZURE_TENANT_ID%"

                    az account set --subscription "%AZURE_SUBSCRIPTION_ID%"

                    az webapp up ^
                        --name myDotnetApp ^
                        --resource-group myResourceGroup ^
                        --runtime "DOTNET:6" ^
                        --src-path published ^
                        --location "East US"
                    '''
                }
            }
        }
    }
}

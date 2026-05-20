pipeline {

 agent any

 environment {
	ACR_NAME = "mytestprojcontanerreg001.azurecr.io"
	IMAGE_NAME = "dotnetapp"
	TAG = "${BUILD_NUMBER}"
	
  }

 stages {

     stage ('Checkout'){
	steps{
          git branch: 'main',
	  url: 'https://github.com/mohamedzaid111/Jenkins_Test02.git'
	   }

	}

     stage ('Restore'){
	 steps{
          sh 'dotnet restore'
	   }

	}

     stage ('Build'){
	steps{
          sh 'dotnet build --configuration Release'
	   }

      }

     stage ('Run unit tests'){
	steps{
          sh 'dotnet test'
	   }

      }

    stage ('Docker Build'){
	steps{
          sh '''
	    docker build -t $ACR_NAME/$IMAGE_NAME:$TAG .

          '''
	   }

      }


     stage('Fetch Secrets') {

            steps {

                withAzureKeyvault(
                    azureKeyVaultSecrets: [

                        [
                            secretType: 'Secret',
                            name: 'acr-username',
                            envVariable: 'ACR_USER'
                        ],

                        [
                            secretType: 'Secret',
                            name: 'acr-password',
                            envVariable: 'ACR_PASS'
                        ]

                    ],

                    keyVaultURL: 'https://jenkins-kv01.vault.azure.net/',
                    credentialID: 'azure-sp'
                ) {

                    sh '''
                    docker login $ACR_NAME \
                        -u $ACR_USER \
                        -p $ACR_PASS
		    docker push $ACR_NAME/$IMAGE_NAME:$TAG
                    '''
                }
            }
        }



   }

}
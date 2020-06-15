node { /* .. snip .. */
	//Define all variables
	
	def location = 'westus2'
	def RgName = 'CPNetNonProd001'
	def az_login = 'Azure-SP'
	def AZURE_DEPLOYMENT_TEMPLATE_FILE: 'deployment.json'
	def AZURE_DEPLOYMENT_PARAMETERS: 'deployment.parameters.json'

	stage('Clone GitHub repository') {
        /* Let's make sure we have the repository cloned to our workspace */
        checkout scm
    }
	
	stage('Deploy Azure Networks'){
		azureCLI commands: [[script: "az group create -n ${RgName} --location ${location}"]], principalCredentialId: 'Azure-SP'
		azureCLI commands: [[script: "az network vnet create --name 'SecVnetUSWest' --resource-group ${RgName} --location ${location} --address-prefix '172.30.0.0/16' --subnet-name 'VMSS-Inspect' --subnet-prefix '172.30.100.0/24'"]], principalCredentialId: 'Azure-SP'
		azureCLI commands: [[script: "az network vnet subnet create --address-prefix '172.30.200.0/24' --name 'VMSS-BackEnd' --resource-group ${RgName} --vnet-name 'SecVnetUSWest'"]], principalCredentialId: 'Azure-SP'
		azureCLI commands: [[script: "az network vnet subnet create --address-prefix '172.30.244.0/24' --name 'Management' --resource-group ${RgName} --vnet-name 'SecVnetUSWest'"]], principalCredentialId: 'Azure-SP'
		azureCLI commands: [[script: "az network vnet subnet create --address-prefix '172.30.220.0/24' --name 'VMSS-WorkLoad' --resource-group ${RgName} --vnet-name 'SecVnetUSWest'"]], principalCredentialId: 'Azure-SP'
	}
	
	
	RgName = 'CPMgmtNonProd001'
	AZURE_DEPLOYMENT_TEMPLATE_FILE: '/var/lib/jenkins/workspace/CICD-Azure/cpmgmt/template.json'
	AZURE_DEPLOYMENT_PARAMETERS: '/var/lib/jenkins/workspace/CICD-Azure/cpmgmt/parameters.json'
	
	
	
	stage('Deploy Azure Management'){
		withCredentials([azureServicePrincipal('Azure-SP')]) {
			sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID'
			sh "az group create --name $RgName --location $location"
			sh "az deployment group create --resource-group $RgName --name cpmgmtwest --template-file $AZURE_DEPLOYMENT_TEMPLATE_FILE --parameters $AZURE_DEPLOYMENT_PARAMETERS"
		}
	}
	
}

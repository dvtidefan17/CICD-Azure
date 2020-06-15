node { /* .. snip .. */
	//Define all variables
	
	def location = 'westus2'
	def RgName = 'CPNetNonProd001'
	def az_login = 'Azure-SP'


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
	
	
	stage('Deploy Azure Management'){
		azureCLI commands: [[script: "az group create -n ${RgName} --location ${location}"]], principalCredentialId: 'Azure-SP'
		azureCLI commands: [[script: "az deployment group create --resource-group ${RgName} --name cpmgmtwest --template-file '/var/lib/jenkins/workspace/CICD-Azure/template-cpmgmt.json' --parameters '{"location": {"value": "westus2"},"cloudGuardVersion": {"value": "R80.30 - Bring Your Own License"},"adminPassword": {"value": "T5zy9AJnjwej"},"authenticationType": {"value": "password"},"vmName": {"value": "cpmgmt01"}}']], principalCredentialId: 'Azure-SP'
	}
	
}

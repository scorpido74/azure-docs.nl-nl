---
title: Een Service Fabric-cluster maken met behulp van Azure Resource Manager sjabloon
description: In deze Quick Start maakt u een Azure Service Fabric test cluster met behulp van Azure Resource Manager sjabloon.
author: erikadoyle
ms.service: service-fabric
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: edoyle
ms.date: 04/24/2020
ms.openlocfilehash: 60771d5a188df5dfeca3530a551a116c870e63f5
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82149331"
---
# <a name="quickstart-create-a-service-fabric-cluster-using-resource-manager-template"></a>Snelstartgids: een Service Fabric-cluster maken met Resource Manager-sjabloon

Azure Service Fabric is een platform voor gedistribueerde systemen waarmee u gemakkelijk schaalbare en betrouwbare microservices en containers verpakt, implementeert en beheert. Een Service Fabric *cluster* is een met het netwerk verbonden reeks virtuele machines waarop uw micro services worden geïmplementeerd en beheerd.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

In dit artikel wordt beschreven hoe u een Service Fabric test cluster in azure implementeert met behulp van Resource Manager. Dit Windows-cluster met vijf knoop punten wordt beveiligd met een zelfondertekend certificaat en is daarom alleen bedoeld voor instructie doeleinden (in plaats van productie werk belastingen).

We gebruiken Azure PowerShell om de sjabloon te implementeren. Naast Azure PowerShell, kunt u ook de Azure Portal, Azure CLI en REST API gebruiken. Zie voor meer informatie over andere implementatie methoden [sjablonen implementeren](../azure-resource-manager/templates/deploy-portal.md).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis](https://azure.microsoft.com/free/) account aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

### <a name="install-service-fabric-sdk-and-powershell-modules"></a>Service Fabric SDK-en Power shell-modules installeren

Als u deze Snelstartgids wilt volt ooien, moet u het volgende doen:

* Installeer de [service Fabric SDK en Power shell-module](service-fabric-get-started.md).

* Installeer [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

### <a name="download-the-sample-template-and-certificate-helper-script"></a>De voorbeeld sjabloon en het Help-script voor het certificaat downloaden

Kloon of down load de [Azure Resource Manager Quick](https://github.com/Azure/azure-quickstart-templates) start-sjablonen opslag plaats. U kunt ook lokaal de volgende bestanden kopiëren die in de map *service-Fabric-Secure-cluster-5-node-1-NodeType* worden gebruikt:

* [New-ServiceFabricClusterCertificate. ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/New-ServiceFabricClusterCertificate.ps1)
* [azuredeploy. json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json)
* [azuredeploy.parameters.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.parameters.json)

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure en wijs het abonnement aan dat moet worden gebruikt voor het maken van uw Service Fabric-cluster.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

### <a name="create-a-self-signed-certificate-stored-in-key-vault"></a>Een zelfondertekend certificaat maken dat is opgeslagen in Key Vault

Service Fabric gebruikt X. 509-certificaten voor [het beveiligen van een cluster en het](./service-fabric-cluster-security.md) bieden van beveiligings functies voor toepassingen en [Key Vault](../key-vault/general/overview.md) voor het beheren van deze certificaten. Voor het maken van een cluster is een cluster certificaat vereist om de communicatie tussen knoop punten in te scha kelen. Voor het maken van deze Snelstartgids test cluster maken we een zelfondertekend certificaat voor cluster authenticatie. Voor productie werkbelastingen zijn certificaten vereist die zijn gemaakt met een correct geconfigureerde Windows Server Certificate Service of een van een goedgekeurde certificerings instantie (CA).

```powershell
# Designate unique (within cloudapp.azure.com) names for your resources
$resourceGroupName = "SFQuickstartRG"
$keyVaultName = "SFQuickstartKV"

# Create a new resource group for your Key Vault and Service Fabric cluster
New-AzResourceGroup -Name $resourceGroupName -Location SouthCentralUS

# Create a Key Vault enabled for deployment
New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $resourceGroupName -Location SouthCentralUS -EnabledForDeployment

# Generate a certificate and upload it to Key Vault
.\New-ServiceFabricClusterCertificate.ps1
```

Het script vraagt u om het volgende te doen (zorg ervoor dat u de *CertDNSName* en de sleutel *kluis* wijzigt in de onderstaande voorbeeld waarden):

* **Wacht woord:** Wacht woord. 1
* **CertDNSName:** *sfquickstart*. southcentralus.cloudapp.Azure.com
* Sleutel **kluisnaam:** *SFQuickstartKV*
* **KeyVaultSecretName:** clustercert

Wanneer het script is voltooid, worden de parameter waarden verstrekt die nodig zijn voor het implementeren van de sjabloon. Zorg ervoor dat u deze in de volgende variabelen opslaat, omdat deze nodig zijn om de cluster sjabloon te implementeren:

```powershell
$sourceVaultId = "<Source Vault Resource Id>"
$certUrlValue = "<Certificate URL>"
$certThumbprint = "<Certificate Thumbprint>"
```

## <a name="create-a-service-fabric-cluster"></a>Een Service Fabric-cluster maken

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype)start-sjablonen. De sjabloon voor dit artikel is te lang om hier weer te geven. Zie https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.jsonvoor het weer geven van de sjabloon.

Er zijn meerdere Azure-resources gedefinieerd in de sjabloon:

* [Micro soft. Storage/Storage accounts](/azure/templates/microsoft.storage/storageaccounts)
* [Micro soft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Micro soft. Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Micro soft. Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)
* [Micro soft. ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)

Zie [Azure Quick](https://azure.microsoft.com/resources/templates/?sort=Popular&term=service+fabric)start-sjablonen voor meer informatie over de sjablonen die betrekking hebben op Azure service Fabric.

### <a name="customize-the-parameters-file"></a>Het parameter bestand aanpassen

Open *azuredeploy. para meters. json* en bewerk de parameter waarden zodat:

* **clustername** komt overeen met de waarde die u hebt opgegeven voor *CertDNSName* bij het maken van het cluster certificaat
* **adminUserName** is een andere waarde dan het standaard *-unieke gen-* token
* **adminPassword** is een andere waarde dan het standaard token voor *gen-Password*
* **certificateThumbprint**, **sourceVaultResourceId**en **certificateUrlValue** zijn alle lege teken reeksen (`""`)

Bijvoorbeeld:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "sfquickstart"
    },
    "adminUsername": {
      "value": "testadm"
    },
    "adminPassword": {
      "value": "Password#1234"
    },
    "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultResourceId": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    }
  }
}
```

## <a name="deploy-the-template"></a>De sjabloon implementeren

Sla de paden van uw Resource Manager-sjabloon en parameter bestanden op in variabelen en implementeer vervolgens de sjabloon.

```powershell
$templateFilePath = "<full path to azuredeploy.json>"
$parameterFilePath = "<full path to azuredeploy.parameters.json>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $certThumbprint `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultResourceId $sourceVaultId `
    -Verbose
```

## <a name="review-deployed-resources"></a>Geïmplementeerde resources controleren

Zodra de implementatie is voltooid, zoekt u `managementEndpoint` de waarde in de uitvoer en opent u het adres in een webbrowser om uw cluster in [service Fabric Explorer](./service-fabric-visualizing-your-cluster.md)weer te geven.

![Service Fabric Explorer het nieuwe cluster weer geven](./media/quickstart-cluster-template/service-fabric-explorer.png)

U kunt ook het Service Fabric Explorer-eind punt vinden op de Blade resource van de service Explorer in Azure Portal.

![Service Fabric resource-Blade met Service Fabric Explorer eind punt](./media/quickstart-cluster-template/service-fabric-explorer-endpoint-azure-portal.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resource groep, waarmee de resources in de resource groep worden verwijderd.

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het maken van een aangepaste Azure Service Fabric-cluster sjabloon:

> [!div class="nextstepaction"]
> [Een Service Fabric cluster resource manager-sjabloon maken](service-fabric-cluster-creation-create-template.md)

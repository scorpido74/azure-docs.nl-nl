---
title: Een virtuele machine maken in DevTest Labs met Azure PowerShell
description: Meer informatie over het gebruik van Azure DevTest Labs om virtuele machines te maken en te beheren met Azure PowerShell.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 13014c39641203bddadf858c34cff67462b3a4b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167113"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Een virtuele machine maken met DevTest Labs met Azure PowerShell
In dit artikel ziet u hoe u een virtuele machine maakt in Azure DevTest Labs met Azure PowerShell. U PowerShell-scripts gebruiken om het maken van virtuele machines in een lab in Azure DevTest Labs te automatiseren. 

## <a name="prerequisites"></a>Vereisten
Voordat u begint:

- [Maak een lab](devtest-lab-create-lab.md) als u geen bestaand lab wilt gebruiken om het script of de opdrachten in dit artikel te testen. 
- [Installeer Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0) of gebruik Azure Cloud Shell die is geïntegreerd in de Azure-portal. 

## <a name="powershell-script"></a>PowerShell-script
Het voorbeeldscript in deze sectie gebruikt de cmdlet [Invoke-AzResourceAction.](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0)  Deze cmdlet neemt de resource-ID van het lab, de naam van de actie uit te voeren (`createEnvironment`) en de parameters die nodig zijn uit te voeren die actie. De parameters bevinden zich in een hashtabel die alle eigenschappen van de virtuele machinebeschrijving bevat. 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "expirationDate"          = "2019-12-01"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

De eigenschappen voor de virtuele machine in het bovenstaande script stellen ons in staat om een virtuele machine te maken met Windows Server 2016 DataCenter als besturingssysteem. Voor elk type virtuele machine zullen deze eigenschappen iets anders zijn. In de sectie [Virtuele machine definiëren](#define-virtual-machine) ziet u hoe u bepalen welke eigenschappen u in dit script wilt gebruiken.

De volgende opdracht geeft een voorbeeld van het uitvoeren van het script dat is opgeslagen in een bestandsnaam: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Virtuele machine definiëren
In deze sectie ziet u hoe u de eigenschappen krijgt die specifiek zijn voor een type virtuele machine dat u wilt maken. 

### <a name="use-azure-portal"></a>Azure Portal gebruiken
U een Azure Resource Manager-sjabloon genereren bij het maken van een vm in de Azure-portal. U hoeft het proces van het maken van de VM niet te voltooien. U volgt de stappen alleen totdat u de sjabloon ziet. Dit is de beste manier om de benodigde JSON-beschrijving te krijgen als u nog geen lab-VM hebt gemaakt. 

1. Navigeer naar de [Azure-portal](https://portal.azure.com).
2. Selecteer **Alle services** in het linkernavigatiemenu.
3. Zoek naar en selecteer **DevTest Labs** in de lijst met services. 
4. Selecteer op de pagina **DevTest Labs** uw lab in de lijst met labs.
5. Selecteer **+ Toevoegen** op de werkbalk op de startpagina voor uw lab. 
6. Selecteer een **basisafbeelding** voor de VM. 
7. Selecteer **automatiseringsopties** onder aan de pagina boven de knop **Verzenden.** 
8. U ziet de **sjabloon Azure Resource Manager** voor het maken van de virtuele machine. 
9. Het JSON-segment in de sectie **Resources** heeft de definitie voor het afbeeldingstype dat u eerder hebt geselecteerd. 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

In dit voorbeeld ziet u hoe u een definitie van een Azure Market Place-afbeelding krijgt. U een definitie van een aangepaste afbeelding, een formule of een omgeving op dezelfde manier krijgen. Voeg alle artefacten toe die nodig zijn voor de virtuele machine en stel de vereiste geavanceerde instellingen in. Nadat u waarden hebt opgemaakt voor de vereiste velden en eventuele optionele velden, selecteert u de knop **Automatiseringsopties.**

### <a name="use-azure-rest-api"></a>Azure REST API gebruiken
De volgende procedure geeft u stappen om eigenschappen van een afbeelding te krijgen met behulp van de REST API: deze stappen werken alleen voor een bestaande VM in een lab. 

1. Navigeer naar de [lijstpagina Virtuele machines -](/rest/api/dtl/virtualmachines/list) selecteer de knop **Proberen.** 
2. Selecteer uw **Azure-abonnement**.
3. Voer de **resourcegroep voor het lab in.**
4. Voer de **naam van het lab in.** 
5. Selecteer **Uitvoeren**.
6. U ziet de **eigenschappen voor de afbeelding** op basis waarvan de VM is gemaakt. 

## <a name="set-expiration-date"></a>Vervaldatum instellen
In scenario's zoals training, demo's en proefversies u virtuele machines maken en deze automatisch verwijderen na een vaste duur, zodat u geen onnodige kosten hoeft te maken. U een vervaldatum voor een virtuele machine instellen terwijl u deze maakt met PowerShell, zoals wordt weergegeven in het voorbeeld [PowerShell-scriptgedeelte.](#powershell-script)

Hier is een voorbeeld van PowerShell-script waarin de vervaldatum voor alle bestaande VM's in een lab wordt ingesteld:

```powershell
# Values to change
$subscriptionId = '<Enter the subscription Id that contains lab>'
$labResourceGroup = '<Enter the lab resource group>'
$labName = '<Enter the lab name>'
$VmName = '<Enter the VmName>'
$expirationDate = '<Enter the expiration date e.g. 2019-12-16>'

# Log into your Azure account
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId
$VmResourceId = "subscriptions/$subscriptionId/resourcegroups/$labResourceGroup/providers/microsoft.devtestlab/labs/$labName/virtualmachines/$VmName"

$vm = Get-AzureRmResource -ResourceId $VmResourceId -ExpandProperties

# Get all the Vm properties
$VmProperties = $vm.Properties

# Set the expirationDate property
If ($VmProperties.expirationDate -eq $null) {
    $VmProperties | Add-Member -MemberType NoteProperty -Name expirationDate -Value $expirationDate
} Else {
    $VmProperties.expirationDate = $expirationDate
}

Set-AzureRmResource -ResourceId $VmResourceId -Properties $VmProperties -Force
```


## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende inhoud: [Azure PowerShell-documentatie voor Azure DevTest Labs](/powershell/module/az.devtestlabs/)

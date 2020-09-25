---
title: Validatie van installatie kopie van virtuele Azure-machine-Azure Marketplace
description: Meer informatie over het testen en verzenden van een aanbieding voor de virtuele machine in de commerciële Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: 751fbbb83f1ccb75cb84453f8c03296f6d1a786c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91275776"
---
# <a name="azure-virtual-machine-image-validation"></a>Validatie van installatie kopie van virtuele Azure-machine

In dit artikel wordt beschreven hoe u een installatie kopie van een virtuele machine (VM) in de commerciële Marketplace kunt testen en verzenden om te controleren of deze voldoet aan de meest recente publicatie vereisten voor Azure Marketplace.

Voer de volgende stappen uit voordat u uw VM-aanbieding verzendt:

- Implementeer een Azure VM met behulp van uw gegeneraliseerde installatie kopie. Zie hier voor meer informatie over [gegeneraliseerde installatie kopieën](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-vm-technical-asset#generalize-the-image).
- Voer validaties uit.

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Een Azure-VM implementeren met behulp van uw gegeneraliseerde installatie kopie

In deze sectie wordt beschreven hoe u een gegeneraliseerde virtuele harde schijf (VHD)-installatie kopie implementeert voor het maken van een nieuwe Azure VM-resource. Voor dit proces gebruiken we de meegeleverde Azure Resource Manager sjabloon en Azure PowerShell script.

### <a name="prepare-an-azure-resource-manager-template"></a>Een Azure Resource Manager sjabloon voorbereiden

In deze sectie wordt beschreven hoe u een installatie kopie van een door de gebruiker gedefinieerde virtuele machine (VM) maakt en implementeert. U kunt dit doen door VHD-installatie kopieën van besturings systeem en gegevens schijven te voorzien van een door Azure geïmplementeerde virtuele harde schijf. Met deze stappen implementeert u de virtuele machine met gegeneraliseerde VHD.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Upload uw gegeneraliseerde virtuele harde schijf van het besturings systeem en de Vhd's met gegevens schijven naar uw Azure Storage-account.
3. Op de start pagina selecteert u **een resource maken**, zoekt u naar "sjabloon implementatie" en selecteert u **maken**.
4. Kies **uw eigen sjabloon bouwen in de editor**.

    :::image type="content" source="media/vm/template-deployment.png" alt-text="Toont de selectie van een sjabloon.":::

5. Plak de volgende JSON-sjabloon in de editor en selecteer **Opslaan**.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "String"
        },
        "userStorageContainerName": {
            "defaultValue": "vhds",
            "type": "String"
        },
        "dnsNameForPublicIP": {
            "type": "String"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "String"
        },
        "adminPassword": {
            "defaultValue": "",
            "type": "SecureString"
        },
        "osType": {
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ],
            "type": "String"
        },
        "subscriptionId": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "vmSize": {
            "type": "String"
        },
        "publicIPAddressName": {
            "type": "String"
        },
        "vmName": {
            "type": "String"
        },
        "virtualNetworkName": {
            "type": "String"
        },
        "nicName": {
            "type": "String"
        },
        "vhdUrl": {
            "type": "String",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
    "variables": {
        "addressPrefix": "10.0.0.0/16",
        "subnet1Name": "Subnet-1",
        "subnet2Name": "Subnet-2",
        "subnet1Prefix": "10.0.0.0/24",
        "subnet2Prefix": "10.0.1.0/24",
        "publicIPAddressType": "Dynamic",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
        "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
        "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
        "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
    },
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2015-06-15",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2015-06-15",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnet1Name')]",
                        "properties": {
                            "addressPrefix": "[variables('subnet1Prefix')]"
                        }
                    },
                    {
                        "name": "[variables('subnet2Name')]",
                        "properties": {
                            "addressPrefix": "[variables('subnet2Prefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2015-06-15",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnet1Ref')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2015-06-15",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[parameters('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "name": "[concat(parameters('vmName'),'-osDisk')]",
                        "osType": "[parameters('osType')]",
                        "caching": "ReadWrite",
                        "image": {
                            "uri": "[parameters('vhdUrl')]"
                        },
                        "vhd": {
                            "uri": "[variables('osDiskVhdName')]"
                        },
                        "createOption": "FromImage"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "apiVersion": "2015-06-15",
            "name": "[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "typeHandlerVersion": "1.4",
                "settings": {
                    "fileUris": [
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd"
                    ],
                    "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]"
                }
            }
        }
    ]
}
```

<br>

6. Geef de parameter waarden op voor de weer gegeven eigenschappen pagina's met aangepaste implementatie.

| ResourceGroupName | De naam van de bestaande Azure-resource groep. Gebruik normaal gesp roken dezelfde RG als uw sleutel kluis. |
| --- | --- |
| TemplateFile | Volledige padnaam naar het bestand VHDtoImage.jsop. |
| userStorageAccountName | Naam van het opslagaccount. |
| dnsNameForPublicIP | DNS-naam voor het open bare IP-adres. moet een kleine letter zijn. |
| subscriptionId | Azure-abonnements-id. |
| Locatie | Standaard geografische locatie van Azure van de resource groep. |
| vmName | De naam van de virtuele machine. |
| vhdUrl | Het webadres van de virtuele harde schijf. |
| vmSize | Grootte van het exemplaar van de virtuele machine. |
| publicIPAddressName | De naam van het open bare IP-adres. |
| virtualNetworkName | De naam van het virtuele netwerk. |
| nicName | De naam van de netwerk interface kaart voor het virtuele netwerk. |
| adminUserName | De gebruikers naam van het Administrator-account. |
| adminPassword | Beheerders wachtwoord. |
|

7. Nadat u deze waarden hebt opgegeven, selecteert u **kopen**.

De implementatie van Azure wordt gestart. Er wordt een nieuwe virtuele machine gemaakt met de opgegeven onbeheerde VHD in het opgegeven pad van het opslag account. U kunt de voortgang van de Azure Portal volgen door **virtual machines** aan de linkerkant van de portal te selecteren. Wanneer de virtuele machine wordt gemaakt, wordt de status gewijzigd van gestart in actief.

#### <a name="for-generation-2-vm-deployment-use-this-template"></a>Voor de VM-implementatie van generatie 2 gebruikt u deze sjabloon:

```JSON
{
   "$schema":"https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{
      "userStorageAccountName":{
         "type":"String"
      },
      "userStorageContainerName":{
         "type":"String",
         "defaultValue":"vhds-86350-720-f4efbbb2-611b-4cd7-ad1e-afdgfuadfluad"
      },
      "dnsNameForPublicIP":{
         "type":"String"
      },
      "adminUserName":{
         "defaultValue":"isv",
         "type":"String"
      },
      "adminPassword":{
         "type":"securestring",
         "defaultValue":"Certcare@86350"
      },
      "osType":{
         "type":"string",
         "defaultValue":"linux",
         "allowedValues":[
            "windows",
            "linux"
         ]
      },
      "subscriptionId":{
         "type":"string"
      },
      "location":{
         "type":"string"
      },
      "vmSize":{
         "type":"string"
      },
      "publicIPAddressName":{
         "type":"string"
      },
      "vmName":{
         "type":"string"
      },
      "vNetNewOrExisting":{
         "defaultValue":"existing",
         "allowedValues":[
            "new",
            "existing"
         ],
         "type":"String",
         "metadata":{
            "description":"Specify whether to create a new or existing virtual network for the VM."
         }
      },
      "virtualNetworkName":{
         "type":"String",
         "defaultValue":""
      },
      "SubnetName":{
         "defaultValue":"subnet-5",
         "type":"String"
      },
      "SubnetPrefix":{
         "defaultValue":"10.0.5.0/24",
         "type":"String"
      },
      "nicName":{
         "type":"string"
      },
      "vhdUrl":{
         "type":"string",
         "metadata":{
            "description":"VHD Url..."
         }
      },
      "Datadisk1":{
         "type":"string",
         "metadata":{
            "description":"datadisk1 Url..."
         }
      },
      "Datadisk2":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      },
      "Datadisk3":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      }
   },
   "variables":{
      "addressPrefix":"10.0.0.0/16",
      "subnet1Name":"[parameters('SubnetName')]",
      "subnet1Prefix":"[parameters('SubnetPrefix')]",
      "publicIPAddressType":"Static",
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
      "subnet1Ref":"[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
      "osDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]",
      "dataDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'datadisk')]",
      "imageName":"[concat(parameters('vmName'), '-image')]"
   },
   "resources":[
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/publicIPAddresses",
         "name":"[parameters('publicIPAddressName')]",
         "location":"[parameters('location')]",
         "properties":{
            "publicIPAllocationMethod":"[variables('publicIPAddressType')]",
            "dnsSettings":{
               "domainNameLabel":"[parameters('dnsNameForPublicIP')]"
            }
         }
      },
      {
         "type":"Microsoft.Compute/images",
         "apiVersion":"2019-12-01",
         "name":"[variables('imageName')]",
         "location":"[parameters('location')]",
         "properties":{
            "storageProfile":{
               "osDisk":{
                  "osType":"[parameters('osType')]",
                  "osState":"Generalized",
                  "blobUri":"[parameters('vhdUrl')]",
                  "storageAccountType":"Standard_LRS"
               },
               "dataDisks":[
                  {
                     "lun":0,
                     "blobUri":"[parameters('Datadisk1')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":1,
                     "blobUri":"[parameters('Datadisk2')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":2,
                     "blobUri":"[parameters('Datadisk3')]",
                     "storageAccountType":"Standard_LRS"
                  }
               ]
            },
            "hyperVGeneration":"V2"
         }
      },
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/virtualNetworks",
         "name":"[parameters('virtualNetworkName')]",
         "location":"[parameters('location')]",
         "properties":{
            "addressSpace":{
               "addressPrefixes":[
                  "[variables('addressPrefix')]"
               ]
            },
            "subnets":[
               {
                  "name":"[variables('subnet1Name')]",
                  "properties":{
                     "addressPrefix":"[variables('subnet1Prefix')]"
                  }
               }
            ]
         },
         "condition":"[equals(parameters('vNetNewOrExisting'), 'new')]"
      },
      {
         "apiVersion":"2016-09-01",
         "type":"Microsoft.Network/networkInterfaces",
         "name":"[parameters('nicName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
            "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
         ],
         "properties":{
            "ipConfigurations":[
               {
                  "name":"ipconfig1",
                  "properties":{
                     "privateIPAllocationMethod":"Dynamic",
                     "publicIPAddress":{
                        "id":"[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                     },
                     "subnet":{
                        "id":"[variables('subnet1Ref')]"
                     }
                  }
               }
            ]
         }
      },
      {
         "apiVersion":"2019-12-01",
         "type":"Microsoft.Compute/virtualMachines",
         "name":"[parameters('vmName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]",
            "[variables('imageName')]"
         ],
         "properties":{
            "hardwareProfile":{
               "vmSize":"[parameters('vmSize')]"
            },
            "osProfile":{
               "computername":"[parameters('vmName')]",
               "adminUsername":"[parameters('adminUsername')]",
               "adminPassword":"[parameters('adminPassword')]"
            },
            "storageProfile":{
               "imageReference":{
                  "id":"[resourceId('Microsoft.Compute/images', variables('imageName'))]"
               },
               "dataDisks":[
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk0')]",
                     "lun":0,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk1')]",
                     "lun":1,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk2')]",
                     "lun":2,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  }
               ],
               "osDisk":{
                  "name":"[concat(parameters('vmName'),'-OSDisk')]",
                  "createOption":"FromImage",
                  "managedDisk":{
                     "storageAccountType":"Standard_LRS"
                  }
               }
            },
            "networkProfile":{
               "networkInterfaces":[
                  {
                     "id":"[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                  }
               ]
            }
         }
      }
   ]
}
```

### <a name="deploy-an-azure-vm-using-powershell"></a>Een Azure-VM implementeren met behulp van Power shell

Kopieer en bewerk het volgende script om waarden voor de variabelen en op te geven `$storageaccount` `$vhdUrl` . Voer deze uit om een Azure VM-resource te maken op basis van uw bestaande gegeneraliseerde VHD.

```PowerShell
# storage account of existing generalized VHD

$storageaccount = "testwinrm11815" # generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" - userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" - vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id -vhdUrl"$vhdUrl" - vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" - adminPassword$pwd
```

## <a name="run-validations"></a>Validaties uitvoeren

Er zijn twee manieren om validaties uit te voeren op de geïmplementeerde installatie kopie.

### <a name="use-certification-test-tool-for-azure-certified"></a>Certificerings test hulpprogramma gebruiken voor Azure Certified

#### <a name="download-and-run-the-certification-test-tool"></a>Het hulp programma voor certificerings test downloaden en uitvoeren

Het certificerings test hulpprogramma voor Azure Certified wordt uitgevoerd op een lokale Windows-computer, maar test een op Azure gebaseerde Windows-of Linux-VM. Het verklaart dat uw VM-installatie kopie van uw gebruiker kan worden gebruikt met Microsoft Azure en dat aan de richt lijnen en vereisten rond het voorbereiden van uw VHD is voldaan.

1. Down load en installeer het meest recente [certificerings test programma voor Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299).
2. Open het certificerings programma en selecteer vervolgens **nieuwe test starten**.
3. Voer in het scherm test informatie een **test naam** in voor de test uitvoering.
4. Selecteer het platform voor uw virtuele machine, ofwel **Windows Server** of **Linux**. De platform keuze is van invloed op de resterende opties.
5. Als uw virtuele machine gebruikmaakt van deze database service, schakelt u het selectie vakje **testen voor Azure SQL database** in.

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>Het certificerings hulpprogramma verbinden met een VM-installatie kopie

1. Selecteer de SSH-verificatie modus: wachtwoord verificatie of sleutel bestands verificatie.
2. Als u verificatie op basis van wacht woorden gebruikt, voert u waarden in voor de **DNS-naam van de virtuele machine**, de **gebruikers naam**en het **wacht woord**. U kunt ook het standaard SSH-poort nummer wijzigen.

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="Toont de selectie van VM-test gegevens.":::

3. Als u gebruikmaakt van verificatie op basis van een sleutel bestand, voert u waarden in voor de DNS-naam van de virtuele machine, de gebruikers naam en de locatie van de persoonlijke sleutel. U kunt ook een wachtwoordzin toevoegen of het standaard-SSH-poort nummer wijzigen.
4. Voer de volledig gekwalificeerde VM-DNS-naam in (bijvoorbeeld MyVMName.Cloudapp.net).
5. Voer de **gebruikers naam** en het **wacht woord**in.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Toont de selectie van de gebruikers naam en het wacht woord van de VM.":::

6. Selecteer **Next**.

#### <a name="run-a-certification-test"></a>Een certificerings test uitvoeren

Nadat u de parameter waarden voor uw VM-installatie kopie in het certificerings programma hebt opgegeven, selecteert u verbinding testen om een geldige verbinding met uw virtuele machine te maken. Nadat een verbinding is geverifieerd, selecteert u **volgende** om de test te starten. Wanneer de test is voltooid, worden de resultaten weer gegeven in een tabel. In de kolom Status wordt voor elke test weer gegeven (pass/fail/Warning). Als een van de tests mislukt, is uw installatie kopie niet gecertificeerd. In dit geval raadpleegt u de vereisten en fout berichten, brengt u de voorgestelde wijzigingen aan en voert u de test opnieuw uit.

Nadat de geautomatiseerde test is voltooid, geeft u aanvullende informatie over de VM-installatie kopie op de twee tabbladen van het vragenlijst scherm, algemene evaluatie en aanpassing van de kernel en selecteert u volgende.

In het laatste scherm kunt u meer informatie opgeven, zoals SSH-toegangs gegevens voor een Linux VM-installatie kopie, en een uitleg voor eventuele mislukte beoordelingen als u op zoek bent naar uitzonde ringen.

Selecteer ten slotte rapport genereren om de test resultaten en logboek bestanden voor de uitgevoerde test cases samen met uw antwoorden op de vragen lijst te downloaden. 
> [!Note]
> Enkele uitgevers hebben scenario's waarbij Vm's moeten worden vergrendeld omdat ze software hebben, zoals firewalls die zijn geïnstalleerd op de virtuele machine. In dit geval kunnen uitgevers het [gecertificeerde test programma](https://aka.ms/AzureCertificationTestTool) hier downloaden en het rapport op Marketplace- [Uitgever ondersteuning](https://aka.ms/marketplacepublishersupport) bieden.

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>Power shell gebruiken voor het verbruiken van de zelf test-API

### <a name="on-linux-os"></a>Op Linux-besturings systeem

De API aanroepen in Power shell:

1. Gebruik de opdracht invoke-WebRequest om de API aan te roepen.
2. De methode is post en het inhouds type is JSON, zoals wordt weer gegeven in het volgende code voorbeeld en scherm opname.
3. Geef de hoofd tekst parameters op in JSON-indeling.

In dit volgende voor beeld ziet u een Power shell-aanroep voor de API:

```POWERSHELL
$accesstoken = “token”
$headers = New-Object “System.Collections.Generic.Dictionary[[String],[String]]”
$headers.Add(“Authorization”, “Bearer $accesstoken”)
$DNSName = “\&lt;\&lt;Machine DNS Name\&gt;\&gt;”
$UserName = “\&lt;\&lt;User ID\&gt;\&gt;”
$Password = “\&lt;\&lt;Password\&gt;\&gt;”
$OS = “Linux”
$PortNo = “22”
$CompanyName = “ABCD”
$AppID = “\&lt;\&lt;Application ID\&gt;\&gt;”
$TenantId = “\&lt;\&lt;Tenant ID\&gt;\&gt;”

$body =
@{
DNSName = $DNSName
UserName = $UserName
Password = $Password
OS = $OS
PortNo = $PortNo
CompanyName = $CompanyName
AppID = $AppID
TenantId = $TenantId
}| ConvertTo-Json

$body

$uri = “URL”

$res = (Invoke-WebRequest -Method “Post” -Uri $uri -Body $body -ContentType “application/json” -Headers $headers).Content
```

<br>Hier volgt een voor beeld van het aanroepen van de API in Power shell:

[![Scherm voorbeeld voor het aanroepen van de API in Power shell.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>In het vorige voor beeld kunt u de JSON ophalen en parseren om de volgende details op te halen:

```PowerShell
$resVar=$res|ConvertFrom-Json

$actualresult =$resVar.Response |ConvertFrom-Json

Write-Host”OSName: $($actualresult.OSName)”Write-Host”OSVersion: $($actualresult.OSVersion)”Write-Host”Overall Test Result: $($actualresult.TestResult)”For ($i=0; $i -lt$actualresult.Tests.Length; $i++){ Write-Host”TestID: $($actualresult.Tests[$i].TestID)”Write-Host”TestCaseName: $($actualresult.Tests[$i].TestCaseName)”Write-Host”Description: $($actualresult.Tests[$i].Description)”Write-Host”Result: $($actualresult.Tests[$i].Result)”Write-Host”ActualValue: $($actualresult.Tests[$i].ActualValue)”}
```

<br>In dit voorbeeld scherm, dat toont `$res.Content` , worden de details van de test resultaten weer gegeven in JSON-indeling:

[![Scherm voorbeeld voor het aanroepen van de API in Power shell met details van test resultaten.](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Hier volgt een voor beeld van resultaten van JSON-testen die worden weer gegeven in een online JSON-Viewer (zoals [code beautify](https://codebeautify.org/jsonviewer) of [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Meer test resultaten in een online JSON-viewer.](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>Op Windows-besturings systeem

De API aanroepen in Power shell:

1. Gebruik de opdracht invoke-WebRequest om de API aan te roepen.
2. De methode is post en het inhouds type is JSON, zoals wordt weer gegeven in het volgende code voorbeeld en voor beeld scherm.
3. Maak de hoofd tekst-para meters in JSON-indeling.

Dit code voorbeeld toont een Power shell-aanroep van de API:

```PowerShell
$accesstoken = “Get token for your Client AAD App”$headers = New-Object”System.Collections.Generic.Dictionary[[String],[String]]”$headers.Add(“Authorization”, “Bearer $accesstoken”)$Body = @{ “DNSName” = “XXXX.westus.cloudapp.azure.com”“UserName” = “XXX”“Password” = “XXX@123456”“OS” = “Windows”“PortNo” = “5986”“CompanyName” = “ABCD” “AppID” = “XXXX-XXXX-XXXX” “TenantId” = “XXXX-XXXX-XXXX” } | ConvertTo-Json$res = Invoke-WebRequest -Method”Post” -Uri$uri -Body$Body -ContentType”application/json” –Headers $headers;$Content = $res | ConvertFrom-Json
```

Deze voorbeeld schermen tonen voor beeld voor het aanroepen van de API in Power shell:

**Met SSH-sleutel**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Het aanroepen van de API in Power shell met een SSH-sleutel.":::

**Met wacht woord**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Het aanroepen van de API in Power shell met een wacht woord.":::

<br>In het vorige voor beeld kunt u de JSON ophalen en parseren om de volgende details op te halen:

```PowerShell
$resVar=$res|ConvertFrom-Json

$actualresult =$resVar.Response |ConvertFrom-Json

Write-Host”OSName: $($actualresult.OSName)”Write-Host”OSVersion: $($actualresult.OSVersion)”Write-Host”Overall Test Result: $($actualresult.TestResult)”For ($i=0; $i -lt$actualresult.Tests.Length; $i++){ Write-Host”TestID: $($actualresult.Tests[$i].TestID)”Write-Host”TestCaseName: $($actualresult.Tests[$i].TestCaseName)”Write-Host”Description: $($actualresult.Tests[$i].Description)”Write-Host”Result: $($actualresult.Tests[$i].Result)”Write-Host”ActualValue: $($actualresult.Tests[$i].ActualValue)”}
```

<br>In dit scherm ziet `$res.Content` u de details van de test resultaten in JSON-indeling:

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="Details van test resultaten in JSON-indeling.":::

<br>Hier volgt een voor beeld van test resultaten die worden weer gegeven in een online JSON-Viewer (zoals [code beautify](https://codebeautify.org/jsonviewer) of [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Test resultaten in een online JSON-viewer.](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>KRUL gebruiken om de zelf test-API in Linux-besturings systeem te verbruiken

De API in krul aanroepen:

1. Gebruik de krul opdracht om de API aan te roepen.
2. De methode is post en het inhouds type is JSON, zoals wordt weer gegeven in het volgende code fragment.

```JSON
CURL POST -H “Content-Type:application/json”

-H “Authorization: Bearer XXXXXX-Token-XXXXXXXX”

[https://isvapp.azure-api.net/selftest-vm](https://isvapp.azure-api.net/selftest-vm)

-d ‘{ “DNSName”:”XXXX.westus.cloudapp.azure.com”, “UserName”:”XXX”, “Password”:”XXXX@123456”, “OS”:”Linux”, “PortNo”:”22”, “CompanyName”:”ABCD”, “AppId”:”XXXX-XXXX-XXXX”, “TenantId “XXXX-XXXX-XXXX”}’
```

<br>Hier volgt een voor beeld van het gebruik van krul om de API aan te roepen:

![Voor beeld van het gebruik van krul om de API aan te roepen.](media/vm/use-curl-call-api.png)

<br>Dit is de JSON-resultaten van de krul aanroep:

![De JSON-resultaten van de krul aanroep.](media/vm/test-results-json-viewer-3.png)

## <a name="next-step"></a>Volgende stap

- Lees [veelvoorkomende problemen met SAS-uri's en oplossingen](common-sas-uri-issues.md).

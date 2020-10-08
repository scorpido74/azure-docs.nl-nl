---
title: Een installatie kopie van een virtuele Azure-machine voor Azure Marketplace testen
description: Meer informatie over het testen en verzenden van een Azure virtual machine-aanbieding in micro soft Commercial Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: github-2407
ms.author: krsh
ms.date: 08/14/2020
ms.openlocfilehash: 83fc141a658fb3f5f639d56794c77fe7a3ff28bf
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91821318"
---
# <a name="test-a-virtual-machine-image-for-azure-marketplace"></a>Een installatie kopie van een virtuele machine voor Azure Marketplace testen

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

    :::image type="content" source="media/vm/template-deployment.png" alt-text="Toont de selectie van een sjabloon.&quot;:::

5. Plak de volgende JSON-sjabloon in de editor en selecteer **Opslaan**.

```JSON
{
    &quot;$schema&quot;: &quot;https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#&quot;,
    &quot;contentVersion&quot;: &quot;1.0.0.0&quot;,
    &quot;parameters&quot;: {
        &quot;userStorageAccountName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;userStorageContainerName&quot;: {
            &quot;defaultValue&quot;: &quot;vhds&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;dnsNameForPublicIP&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminUserName&quot;: {
            &quot;defaultValue&quot;: &quot;isv&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminPassword&quot;: {
            &quot;defaultValue&quot;: &quot;&quot;,
            &quot;type&quot;: &quot;SecureString&quot;
        },
        &quot;osType&quot;: {
            &quot;defaultValue&quot;: &quot;windows&quot;,
            &quot;allowedValues&quot;: [
                &quot;windows&quot;,
                &quot;linux&quot;
            ],
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;subscriptionId&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;location&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmSize&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;publicIPAddressName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;virtualNetworkName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;nicName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vhdUrl&quot;: {
            &quot;type&quot;: &quot;String&quot;,
            &quot;metadata&quot;: {
                &quot;description&quot;: &quot;VHD Url...&quot;
            }
        }
    },
    &quot;variables&quot;: {
        &quot;addressPrefix&quot;: &quot;10.0.0.0/16&quot;,
        &quot;subnet1Name&quot;: &quot;Subnet-1&quot;,
        &quot;subnet2Name&quot;: &quot;Subnet-2&quot;,
        &quot;subnet1Prefix&quot;: &quot;10.0.0.0/24&quot;,
        &quot;subnet2Prefix&quot;: &quot;10.0.1.0/24&quot;,
        &quot;publicIPAddressType&quot;: &quot;Dynamic&quot;,
        &quot;vnetID&quot;: &quot;[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]&quot;,
        &quot;subnet1Ref&quot;: &quot;[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]&quot;,
        &quot;hostDNSNameScriptArgument&quot;: &quot;[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]&quot;,
        &quot;osDiskVhdName&quot;: &quot;[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]&quot;
    },
    &quot;resources&quot;: [
        {
            &quot;type&quot;: &quot;Microsoft.Network/publicIPAddresses&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('publicIPAddressName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;publicIPAllocationMethod&quot;: &quot;[variables('publicIPAddressType')]&quot;,
                &quot;dnsSettings&quot;: {
                    &quot;domainNameLabel&quot;: &quot;[parameters('dnsNameForPublicIP')]&quot;
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/virtualNetworks&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('virtualNetworkName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;addressSpace&quot;: {
                    &quot;addressPrefixes&quot;: [
                        &quot;[variables('addressPrefix')]&quot;
                    ]
                },
                &quot;subnets&quot;: [
                    {
                        &quot;name&quot;: &quot;[variables('subnet1Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet1Prefix')]&quot;
                        }
                    },
                    {
                        &quot;name&quot;: &quot;[variables('subnet2Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet2Prefix')]&quot;
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/networkInterfaces&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('nicName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]&quot;,
                &quot;[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;ipConfigurations&quot;: [
                    {
                        &quot;name&quot;: &quot;ipconfig1&quot;,
                        &quot;properties&quot;: {
                            &quot;privateIPAllocationMethod&quot;: &quot;Dynamic&quot;,
                            &quot;publicIPAddress&quot;: {
                                &quot;id&quot;: &quot;[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]&quot;
                            },
                            &quot;subnet&quot;: {
                                &quot;id&quot;: &quot;[variables('subnet1Ref')]&quot;
                            }
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('vmName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;hardwareProfile&quot;: {
                    &quot;vmSize&quot;: &quot;[parameters('vmSize')]&quot;
                },
                &quot;osProfile&quot;: {
                    &quot;computername&quot;: &quot;[parameters('vmName')]&quot;,
                    &quot;adminUsername&quot;: &quot;[parameters('adminUsername')]&quot;,
                    &quot;adminPassword&quot;: &quot;[parameters('adminPassword')]&quot;
                },
                &quot;storageProfile&quot;: {
                    &quot;osDisk&quot;: {
                        &quot;name&quot;: &quot;[concat(parameters('vmName'),'-osDisk')]&quot;,
                        &quot;osType&quot;: &quot;[parameters('osType')]&quot;,
                        &quot;caching&quot;: &quot;ReadWrite&quot;,
                        &quot;image&quot;: {
                            &quot;uri&quot;: &quot;[parameters('vhdUrl')]&quot;
                        },
                        &quot;vhd&quot;: {
                            &quot;uri&quot;: &quot;[variables('osDiskVhdName')]&quot;
                        },
                        &quot;createOption&quot;: &quot;FromImage&quot;
                    }
                },
                &quot;networkProfile&quot;: {
                    &quot;networkInterfaces&quot;: [
                        {
                            &quot;id&quot;: &quot;[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]&quot;
                        }
                    ]
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines/extensions&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;publisher&quot;: &quot;Microsoft.Compute&quot;,
                &quot;type&quot;: &quot;CustomScriptExtension&quot;,
                &quot;typeHandlerVersion&quot;: &quot;1.4&quot;,
                &quot;settings&quot;: {
                    &quot;fileUris&quot;: [
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd&quot;
                    ],
                    &quot;commandToExecute&quot;: &quot;[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]"
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
$storageaccount = "testwinrm11815"
# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd
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

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="Toont de selectie van een sjabloon.&quot;:::

5. Plak de volgende JSON-sjabloon in de editor en selecteer **Opslaan**.

```JSON
{
    &quot;$schema&quot;: &quot;https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#&quot;,
    &quot;contentVersion&quot;: &quot;1.0.0.0&quot;,
    &quot;parameters&quot;: {
        &quot;userStorageAccountName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;userStorageContainerName&quot;: {
            &quot;defaultValue&quot;: &quot;vhds&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;dnsNameForPublicIP&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminUserName&quot;: {
            &quot;defaultValue&quot;: &quot;isv&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminPassword&quot;: {
            &quot;defaultValue&quot;: &quot;&quot;,
            &quot;type&quot;: &quot;SecureString&quot;
        },
        &quot;osType&quot;: {
            &quot;defaultValue&quot;: &quot;windows&quot;,
            &quot;allowedValues&quot;: [
                &quot;windows&quot;,
                &quot;linux&quot;
            ],
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;subscriptionId&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;location&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmSize&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;publicIPAddressName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;virtualNetworkName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;nicName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vhdUrl&quot;: {
            &quot;type&quot;: &quot;String&quot;,
            &quot;metadata&quot;: {
                &quot;description&quot;: &quot;VHD Url...&quot;
            }
        }
    },
    &quot;variables&quot;: {
        &quot;addressPrefix&quot;: &quot;10.0.0.0/16&quot;,
        &quot;subnet1Name&quot;: &quot;Subnet-1&quot;,
        &quot;subnet2Name&quot;: &quot;Subnet-2&quot;,
        &quot;subnet1Prefix&quot;: &quot;10.0.0.0/24&quot;,
        &quot;subnet2Prefix&quot;: &quot;10.0.1.0/24&quot;,
        &quot;publicIPAddressType&quot;: &quot;Dynamic&quot;,
        &quot;vnetID&quot;: &quot;[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]&quot;,
        &quot;subnet1Ref&quot;: &quot;[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]&quot;,
        &quot;hostDNSNameScriptArgument&quot;: &quot;[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]&quot;,
        &quot;osDiskVhdName&quot;: &quot;[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]&quot;
    },
    &quot;resources&quot;: [
        {
            &quot;type&quot;: &quot;Microsoft.Network/publicIPAddresses&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('publicIPAddressName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;publicIPAllocationMethod&quot;: &quot;[variables('publicIPAddressType')]&quot;,
                &quot;dnsSettings&quot;: {
                    &quot;domainNameLabel&quot;: &quot;[parameters('dnsNameForPublicIP')]&quot;
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/virtualNetworks&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('virtualNetworkName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;addressSpace&quot;: {
                    &quot;addressPrefixes&quot;: [
                        &quot;[variables('addressPrefix')]&quot;
                    ]
                },
                &quot;subnets&quot;: [
                    {
                        &quot;name&quot;: &quot;[variables('subnet1Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet1Prefix')]&quot;
                        }
                    },
                    {
                        &quot;name&quot;: &quot;[variables('subnet2Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet2Prefix')]&quot;
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/networkInterfaces&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('nicName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]&quot;,
                &quot;[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;ipConfigurations&quot;: [
                    {
                        &quot;name&quot;: &quot;ipconfig1&quot;,
                        &quot;properties&quot;: {
                            &quot;privateIPAllocationMethod&quot;: &quot;Dynamic&quot;,
                            &quot;publicIPAddress&quot;: {
                                &quot;id&quot;: &quot;[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]&quot;
                            },
                            &quot;subnet&quot;: {
                                &quot;id&quot;: &quot;[variables('subnet1Ref')]&quot;
                            }
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('vmName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;hardwareProfile&quot;: {
                    &quot;vmSize&quot;: &quot;[parameters('vmSize')]&quot;
                },
                &quot;osProfile&quot;: {
                    &quot;computername&quot;: &quot;[parameters('vmName')]&quot;,
                    &quot;adminUsername&quot;: &quot;[parameters('adminUsername')]&quot;,
                    &quot;adminPassword&quot;: &quot;[parameters('adminPassword')]&quot;
                },
                &quot;storageProfile&quot;: {
                    &quot;osDisk&quot;: {
                        &quot;name&quot;: &quot;[concat(parameters('vmName'),'-osDisk')]&quot;,
                        &quot;osType&quot;: &quot;[parameters('osType')]&quot;,
                        &quot;caching&quot;: &quot;ReadWrite&quot;,
                        &quot;image&quot;: {
                            &quot;uri&quot;: &quot;[parameters('vhdUrl')]&quot;
                        },
                        &quot;vhd&quot;: {
                            &quot;uri&quot;: &quot;[variables('osDiskVhdName')]&quot;
                        },
                        &quot;createOption&quot;: &quot;FromImage&quot;
                    }
                },
                &quot;networkProfile&quot;: {
                    &quot;networkInterfaces&quot;: [
                        {
                            &quot;id&quot;: &quot;[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]&quot;
                        }
                    ]
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines/extensions&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;publisher&quot;: &quot;Microsoft.Compute&quot;,
                &quot;type&quot;: &quot;CustomScriptExtension&quot;,
                &quot;typeHandlerVersion&quot;: &quot;1.4&quot;,
                &quot;settings&quot;: {
                    &quot;fileUris&quot;: [
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd&quot;
                    ],
                    &quot;commandToExecute&quot;: &quot;[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]":::

3. Als u gebruikmaakt van verificatie op basis van een sleutel bestand, voert u waarden in voor de DNS-naam van de virtuele machine, de gebruikers naam en de locatie van de persoonlijke sleutel. U kunt ook een wachtwoordzin toevoegen of het standaard-SSH-poort nummer wijzigen.
4. Voer de volledig gekwalificeerde VM-DNS-naam in (bijvoorbeeld MyVMName.Cloudapp.net).
5. Voer de **gebruikers naam** en het **wacht woord**in.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Toont de selectie van een sjabloon.&quot;:::

5. Plak de volgende JSON-sjabloon in de editor en selecteer **Opslaan**.

```JSON
{
    &quot;$schema&quot;: &quot;https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#&quot;,
    &quot;contentVersion&quot;: &quot;1.0.0.0&quot;,
    &quot;parameters&quot;: {
        &quot;userStorageAccountName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;userStorageContainerName&quot;: {
            &quot;defaultValue&quot;: &quot;vhds&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;dnsNameForPublicIP&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminUserName&quot;: {
            &quot;defaultValue&quot;: &quot;isv&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminPassword&quot;: {
            &quot;defaultValue&quot;: &quot;&quot;,
            &quot;type&quot;: &quot;SecureString&quot;
        },
        &quot;osType&quot;: {
            &quot;defaultValue&quot;: &quot;windows&quot;,
            &quot;allowedValues&quot;: [
                &quot;windows&quot;,
                &quot;linux&quot;
            ],
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;subscriptionId&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;location&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmSize&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;publicIPAddressName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;virtualNetworkName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;nicName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vhdUrl&quot;: {
            &quot;type&quot;: &quot;String&quot;,
            &quot;metadata&quot;: {
                &quot;description&quot;: &quot;VHD Url...&quot;
            }
        }
    },
    &quot;variables&quot;: {
        &quot;addressPrefix&quot;: &quot;10.0.0.0/16&quot;,
        &quot;subnet1Name&quot;: &quot;Subnet-1&quot;,
        &quot;subnet2Name&quot;: &quot;Subnet-2&quot;,
        &quot;subnet1Prefix&quot;: &quot;10.0.0.0/24&quot;,
        &quot;subnet2Prefix&quot;: &quot;10.0.1.0/24&quot;,
        &quot;publicIPAddressType&quot;: &quot;Dynamic&quot;,
        &quot;vnetID&quot;: &quot;[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]&quot;,
        &quot;subnet1Ref&quot;: &quot;[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]&quot;,
        &quot;hostDNSNameScriptArgument&quot;: &quot;[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]&quot;,
        &quot;osDiskVhdName&quot;: &quot;[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]&quot;
    },
    &quot;resources&quot;: [
        {
            &quot;type&quot;: &quot;Microsoft.Network/publicIPAddresses&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('publicIPAddressName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;publicIPAllocationMethod&quot;: &quot;[variables('publicIPAddressType')]&quot;,
                &quot;dnsSettings&quot;: {
                    &quot;domainNameLabel&quot;: &quot;[parameters('dnsNameForPublicIP')]&quot;
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/virtualNetworks&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('virtualNetworkName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;addressSpace&quot;: {
                    &quot;addressPrefixes&quot;: [
                        &quot;[variables('addressPrefix')]&quot;
                    ]
                },
                &quot;subnets&quot;: [
                    {
                        &quot;name&quot;: &quot;[variables('subnet1Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet1Prefix')]&quot;
                        }
                    },
                    {
                        &quot;name&quot;: &quot;[variables('subnet2Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet2Prefix')]&quot;
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/networkInterfaces&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('nicName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]&quot;,
                &quot;[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;ipConfigurations&quot;: [
                    {
                        &quot;name&quot;: &quot;ipconfig1&quot;,
                        &quot;properties&quot;: {
                            &quot;privateIPAllocationMethod&quot;: &quot;Dynamic&quot;,
                            &quot;publicIPAddress&quot;: {
                                &quot;id&quot;: &quot;[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]&quot;
                            },
                            &quot;subnet&quot;: {
                                &quot;id&quot;: &quot;[variables('subnet1Ref')]&quot;
                            }
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('vmName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;hardwareProfile&quot;: {
                    &quot;vmSize&quot;: &quot;[parameters('vmSize')]&quot;
                },
                &quot;osProfile&quot;: {
                    &quot;computername&quot;: &quot;[parameters('vmName')]&quot;,
                    &quot;adminUsername&quot;: &quot;[parameters('adminUsername')]&quot;,
                    &quot;adminPassword&quot;: &quot;[parameters('adminPassword')]&quot;
                },
                &quot;storageProfile&quot;: {
                    &quot;osDisk&quot;: {
                        &quot;name&quot;: &quot;[concat(parameters('vmName'),'-osDisk')]&quot;,
                        &quot;osType&quot;: &quot;[parameters('osType')]&quot;,
                        &quot;caching&quot;: &quot;ReadWrite&quot;,
                        &quot;image&quot;: {
                            &quot;uri&quot;: &quot;[parameters('vhdUrl')]&quot;
                        },
                        &quot;vhd&quot;: {
                            &quot;uri&quot;: &quot;[variables('osDiskVhdName')]&quot;
                        },
                        &quot;createOption&quot;: &quot;FromImage&quot;
                    }
                },
                &quot;networkProfile&quot;: {
                    &quot;networkInterfaces&quot;: [
                        {
                            &quot;id&quot;: &quot;[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]&quot;
                        }
                    ]
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines/extensions&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;publisher&quot;: &quot;Microsoft.Compute&quot;,
                &quot;type&quot;: &quot;CustomScriptExtension&quot;,
                &quot;typeHandlerVersion&quot;: &quot;1.4&quot;,
                &quot;settings&quot;: {
                    &quot;fileUris&quot;: [
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd&quot;
                    ],
                    &quot;commandToExecute&quot;: &quot;[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]":::

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
$accesstoken = "token"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$DNSName = "<Machine DNS Name>"
$UserName = "<User ID>"
$Password = "<Password>"
$OS = "Linux"
$PortNo = "22"
$CompanyName = "ABCD"
$AppID = "<Application ID>"
$TenantId = "<Tenant ID>"

$body = @{
   "DNSName" = $DNSName
   "UserName" = $UserName
   "Password" = $Password
   "OS" = $OS
   "PortNo" = $PortNo
   "CompanyName" = $CompanyName
   "AppID" = $AppID
   "TenantId" = $TenantId
} | ConvertTo-Json

$body

$uri = "URL"

$res = (Invoke-WebRequest -Method "Post" -Uri $uri -Body $body -ContentType "application/json" -Headers $headers).Content
```

<br>Hier volgt een voor beeld van het aanroepen van de API in Power shell:

[![Scherm voorbeeld voor het aanroepen van de API in Power shell.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>In het vorige voor beeld kunt u de JSON ophalen en parseren om de volgende details op te halen:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
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
$accesstoken = "Get token for your Client AAD App"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$Body = @{ 
   "DNSName" = "XXXX.westus.cloudapp.azure.com"
   "UserName" = "XXX"
   "Password" = "XXX@123456"
   "OS" = "Windows"
   "PortNo" = "5986"
   "CompanyName" = "ABCD"
   "AppID" = "XXXX-XXXX-XXXX"
   "TenantId" = "XXXX-XXXX-XXXX"
} | ConvertTo-Json

$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

Deze voorbeeld schermen tonen voor beeld voor het aanroepen van de API in Power shell:

**Met SSH-sleutel**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Toont de selectie van een sjabloon.&quot;:::

5. Plak de volgende JSON-sjabloon in de editor en selecteer **Opslaan**.

```JSON
{
    &quot;$schema&quot;: &quot;https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#&quot;,
    &quot;contentVersion&quot;: &quot;1.0.0.0&quot;,
    &quot;parameters&quot;: {
        &quot;userStorageAccountName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;userStorageContainerName&quot;: {
            &quot;defaultValue&quot;: &quot;vhds&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;dnsNameForPublicIP&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminUserName&quot;: {
            &quot;defaultValue&quot;: &quot;isv&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminPassword&quot;: {
            &quot;defaultValue&quot;: &quot;&quot;,
            &quot;type&quot;: &quot;SecureString&quot;
        },
        &quot;osType&quot;: {
            &quot;defaultValue&quot;: &quot;windows&quot;,
            &quot;allowedValues&quot;: [
                &quot;windows&quot;,
                &quot;linux&quot;
            ],
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;subscriptionId&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;location&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmSize&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;publicIPAddressName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;virtualNetworkName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;nicName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vhdUrl&quot;: {
            &quot;type&quot;: &quot;String&quot;,
            &quot;metadata&quot;: {
                &quot;description&quot;: &quot;VHD Url...&quot;
            }
        }
    },
    &quot;variables&quot;: {
        &quot;addressPrefix&quot;: &quot;10.0.0.0/16&quot;,
        &quot;subnet1Name&quot;: &quot;Subnet-1&quot;,
        &quot;subnet2Name&quot;: &quot;Subnet-2&quot;,
        &quot;subnet1Prefix&quot;: &quot;10.0.0.0/24&quot;,
        &quot;subnet2Prefix&quot;: &quot;10.0.1.0/24&quot;,
        &quot;publicIPAddressType&quot;: &quot;Dynamic&quot;,
        &quot;vnetID&quot;: &quot;[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]&quot;,
        &quot;subnet1Ref&quot;: &quot;[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]&quot;,
        &quot;hostDNSNameScriptArgument&quot;: &quot;[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]&quot;,
        &quot;osDiskVhdName&quot;: &quot;[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]&quot;
    },
    &quot;resources&quot;: [
        {
            &quot;type&quot;: &quot;Microsoft.Network/publicIPAddresses&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('publicIPAddressName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;publicIPAllocationMethod&quot;: &quot;[variables('publicIPAddressType')]&quot;,
                &quot;dnsSettings&quot;: {
                    &quot;domainNameLabel&quot;: &quot;[parameters('dnsNameForPublicIP')]&quot;
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/virtualNetworks&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('virtualNetworkName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;addressSpace&quot;: {
                    &quot;addressPrefixes&quot;: [
                        &quot;[variables('addressPrefix')]&quot;
                    ]
                },
                &quot;subnets&quot;: [
                    {
                        &quot;name&quot;: &quot;[variables('subnet1Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet1Prefix')]&quot;
                        }
                    },
                    {
                        &quot;name&quot;: &quot;[variables('subnet2Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet2Prefix')]&quot;
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/networkInterfaces&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('nicName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]&quot;,
                &quot;[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;ipConfigurations&quot;: [
                    {
                        &quot;name&quot;: &quot;ipconfig1&quot;,
                        &quot;properties&quot;: {
                            &quot;privateIPAllocationMethod&quot;: &quot;Dynamic&quot;,
                            &quot;publicIPAddress&quot;: {
                                &quot;id&quot;: &quot;[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]&quot;
                            },
                            &quot;subnet&quot;: {
                                &quot;id&quot;: &quot;[variables('subnet1Ref')]&quot;
                            }
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('vmName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;hardwareProfile&quot;: {
                    &quot;vmSize&quot;: &quot;[parameters('vmSize')]&quot;
                },
                &quot;osProfile&quot;: {
                    &quot;computername&quot;: &quot;[parameters('vmName')]&quot;,
                    &quot;adminUsername&quot;: &quot;[parameters('adminUsername')]&quot;,
                    &quot;adminPassword&quot;: &quot;[parameters('adminPassword')]&quot;
                },
                &quot;storageProfile&quot;: {
                    &quot;osDisk&quot;: {
                        &quot;name&quot;: &quot;[concat(parameters('vmName'),'-osDisk')]&quot;,
                        &quot;osType&quot;: &quot;[parameters('osType')]&quot;,
                        &quot;caching&quot;: &quot;ReadWrite&quot;,
                        &quot;image&quot;: {
                            &quot;uri&quot;: &quot;[parameters('vhdUrl')]&quot;
                        },
                        &quot;vhd&quot;: {
                            &quot;uri&quot;: &quot;[variables('osDiskVhdName')]&quot;
                        },
                        &quot;createOption&quot;: &quot;FromImage&quot;
                    }
                },
                &quot;networkProfile&quot;: {
                    &quot;networkInterfaces&quot;: [
                        {
                            &quot;id&quot;: &quot;[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]&quot;
                        }
                    ]
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines/extensions&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;publisher&quot;: &quot;Microsoft.Compute&quot;,
                &quot;type&quot;: &quot;CustomScriptExtension&quot;,
                &quot;typeHandlerVersion&quot;: &quot;1.4&quot;,
                &quot;settings&quot;: {
                    &quot;fileUris&quot;: [
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd&quot;
                    ],
                    &quot;commandToExecute&quot;: &quot;[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]":::

**Met wacht woord**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Toont de selectie van een sjabloon.&quot;:::

5. Plak de volgende JSON-sjabloon in de editor en selecteer **Opslaan**.

```JSON
{
    &quot;$schema&quot;: &quot;https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#&quot;,
    &quot;contentVersion&quot;: &quot;1.0.0.0&quot;,
    &quot;parameters&quot;: {
        &quot;userStorageAccountName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;userStorageContainerName&quot;: {
            &quot;defaultValue&quot;: &quot;vhds&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;dnsNameForPublicIP&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminUserName&quot;: {
            &quot;defaultValue&quot;: &quot;isv&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminPassword&quot;: {
            &quot;defaultValue&quot;: &quot;&quot;,
            &quot;type&quot;: &quot;SecureString&quot;
        },
        &quot;osType&quot;: {
            &quot;defaultValue&quot;: &quot;windows&quot;,
            &quot;allowedValues&quot;: [
                &quot;windows&quot;,
                &quot;linux&quot;
            ],
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;subscriptionId&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;location&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmSize&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;publicIPAddressName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;virtualNetworkName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;nicName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vhdUrl&quot;: {
            &quot;type&quot;: &quot;String&quot;,
            &quot;metadata&quot;: {
                &quot;description&quot;: &quot;VHD Url...&quot;
            }
        }
    },
    &quot;variables&quot;: {
        &quot;addressPrefix&quot;: &quot;10.0.0.0/16&quot;,
        &quot;subnet1Name&quot;: &quot;Subnet-1&quot;,
        &quot;subnet2Name&quot;: &quot;Subnet-2&quot;,
        &quot;subnet1Prefix&quot;: &quot;10.0.0.0/24&quot;,
        &quot;subnet2Prefix&quot;: &quot;10.0.1.0/24&quot;,
        &quot;publicIPAddressType&quot;: &quot;Dynamic&quot;,
        &quot;vnetID&quot;: &quot;[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]&quot;,
        &quot;subnet1Ref&quot;: &quot;[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]&quot;,
        &quot;hostDNSNameScriptArgument&quot;: &quot;[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]&quot;,
        &quot;osDiskVhdName&quot;: &quot;[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]&quot;
    },
    &quot;resources&quot;: [
        {
            &quot;type&quot;: &quot;Microsoft.Network/publicIPAddresses&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('publicIPAddressName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;publicIPAllocationMethod&quot;: &quot;[variables('publicIPAddressType')]&quot;,
                &quot;dnsSettings&quot;: {
                    &quot;domainNameLabel&quot;: &quot;[parameters('dnsNameForPublicIP')]&quot;
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/virtualNetworks&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('virtualNetworkName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;addressSpace&quot;: {
                    &quot;addressPrefixes&quot;: [
                        &quot;[variables('addressPrefix')]&quot;
                    ]
                },
                &quot;subnets&quot;: [
                    {
                        &quot;name&quot;: &quot;[variables('subnet1Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet1Prefix')]&quot;
                        }
                    },
                    {
                        &quot;name&quot;: &quot;[variables('subnet2Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet2Prefix')]&quot;
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/networkInterfaces&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('nicName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]&quot;,
                &quot;[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;ipConfigurations&quot;: [
                    {
                        &quot;name&quot;: &quot;ipconfig1&quot;,
                        &quot;properties&quot;: {
                            &quot;privateIPAllocationMethod&quot;: &quot;Dynamic&quot;,
                            &quot;publicIPAddress&quot;: {
                                &quot;id&quot;: &quot;[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]&quot;
                            },
                            &quot;subnet&quot;: {
                                &quot;id&quot;: &quot;[variables('subnet1Ref')]&quot;
                            }
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('vmName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;hardwareProfile&quot;: {
                    &quot;vmSize&quot;: &quot;[parameters('vmSize')]&quot;
                },
                &quot;osProfile&quot;: {
                    &quot;computername&quot;: &quot;[parameters('vmName')]&quot;,
                    &quot;adminUsername&quot;: &quot;[parameters('adminUsername')]&quot;,
                    &quot;adminPassword&quot;: &quot;[parameters('adminPassword')]&quot;
                },
                &quot;storageProfile&quot;: {
                    &quot;osDisk&quot;: {
                        &quot;name&quot;: &quot;[concat(parameters('vmName'),'-osDisk')]&quot;,
                        &quot;osType&quot;: &quot;[parameters('osType')]&quot;,
                        &quot;caching&quot;: &quot;ReadWrite&quot;,
                        &quot;image&quot;: {
                            &quot;uri&quot;: &quot;[parameters('vhdUrl')]&quot;
                        },
                        &quot;vhd&quot;: {
                            &quot;uri&quot;: &quot;[variables('osDiskVhdName')]&quot;
                        },
                        &quot;createOption&quot;: &quot;FromImage&quot;
                    }
                },
                &quot;networkProfile&quot;: {
                    &quot;networkInterfaces&quot;: [
                        {
                            &quot;id&quot;: &quot;[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]&quot;
                        }
                    ]
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines/extensions&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;publisher&quot;: &quot;Microsoft.Compute&quot;,
                &quot;type&quot;: &quot;CustomScriptExtension&quot;,
                &quot;typeHandlerVersion&quot;: &quot;1.4&quot;,
                &quot;settings&quot;: {
                    &quot;fileUris&quot;: [
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd&quot;
                    ],
                    &quot;commandToExecute&quot;: &quot;[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]":::

<br>In het vorige voor beeld kunt u de JSON ophalen en parseren om de volgende details op te halen:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>In dit scherm ziet `$res.Content` u de details van de test resultaten in JSON-indeling:

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="Toont de selectie van een sjabloon.&quot;:::

5. Plak de volgende JSON-sjabloon in de editor en selecteer **Opslaan**.

```JSON
{
    &quot;$schema&quot;: &quot;https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#&quot;,
    &quot;contentVersion&quot;: &quot;1.0.0.0&quot;,
    &quot;parameters&quot;: {
        &quot;userStorageAccountName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;userStorageContainerName&quot;: {
            &quot;defaultValue&quot;: &quot;vhds&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;dnsNameForPublicIP&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminUserName&quot;: {
            &quot;defaultValue&quot;: &quot;isv&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminPassword&quot;: {
            &quot;defaultValue&quot;: &quot;&quot;,
            &quot;type&quot;: &quot;SecureString&quot;
        },
        &quot;osType&quot;: {
            &quot;defaultValue&quot;: &quot;windows&quot;,
            &quot;allowedValues&quot;: [
                &quot;windows&quot;,
                &quot;linux&quot;
            ],
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;subscriptionId&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;location&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmSize&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;publicIPAddressName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;virtualNetworkName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;nicName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vhdUrl&quot;: {
            &quot;type&quot;: &quot;String&quot;,
            &quot;metadata&quot;: {
                &quot;description&quot;: &quot;VHD Url...&quot;
            }
        }
    },
    &quot;variables&quot;: {
        &quot;addressPrefix&quot;: &quot;10.0.0.0/16&quot;,
        &quot;subnet1Name&quot;: &quot;Subnet-1&quot;,
        &quot;subnet2Name&quot;: &quot;Subnet-2&quot;,
        &quot;subnet1Prefix&quot;: &quot;10.0.0.0/24&quot;,
        &quot;subnet2Prefix&quot;: &quot;10.0.1.0/24&quot;,
        &quot;publicIPAddressType&quot;: &quot;Dynamic&quot;,
        &quot;vnetID&quot;: &quot;[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]&quot;,
        &quot;subnet1Ref&quot;: &quot;[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]&quot;,
        &quot;hostDNSNameScriptArgument&quot;: &quot;[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]&quot;,
        &quot;osDiskVhdName&quot;: &quot;[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]&quot;
    },
    &quot;resources&quot;: [
        {
            &quot;type&quot;: &quot;Microsoft.Network/publicIPAddresses&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('publicIPAddressName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;publicIPAllocationMethod&quot;: &quot;[variables('publicIPAddressType')]&quot;,
                &quot;dnsSettings&quot;: {
                    &quot;domainNameLabel&quot;: &quot;[parameters('dnsNameForPublicIP')]&quot;
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/virtualNetworks&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('virtualNetworkName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;addressSpace&quot;: {
                    &quot;addressPrefixes&quot;: [
                        &quot;[variables('addressPrefix')]&quot;
                    ]
                },
                &quot;subnets&quot;: [
                    {
                        &quot;name&quot;: &quot;[variables('subnet1Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet1Prefix')]&quot;
                        }
                    },
                    {
                        &quot;name&quot;: &quot;[variables('subnet2Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet2Prefix')]&quot;
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/networkInterfaces&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('nicName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]&quot;,
                &quot;[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;ipConfigurations&quot;: [
                    {
                        &quot;name&quot;: &quot;ipconfig1&quot;,
                        &quot;properties&quot;: {
                            &quot;privateIPAllocationMethod&quot;: &quot;Dynamic&quot;,
                            &quot;publicIPAddress&quot;: {
                                &quot;id&quot;: &quot;[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]&quot;
                            },
                            &quot;subnet&quot;: {
                                &quot;id&quot;: &quot;[variables('subnet1Ref')]&quot;
                            }
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('vmName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;hardwareProfile&quot;: {
                    &quot;vmSize&quot;: &quot;[parameters('vmSize')]&quot;
                },
                &quot;osProfile&quot;: {
                    &quot;computername&quot;: &quot;[parameters('vmName')]&quot;,
                    &quot;adminUsername&quot;: &quot;[parameters('adminUsername')]&quot;,
                    &quot;adminPassword&quot;: &quot;[parameters('adminPassword')]&quot;
                },
                &quot;storageProfile&quot;: {
                    &quot;osDisk&quot;: {
                        &quot;name&quot;: &quot;[concat(parameters('vmName'),'-osDisk')]&quot;,
                        &quot;osType&quot;: &quot;[parameters('osType')]&quot;,
                        &quot;caching&quot;: &quot;ReadWrite&quot;,
                        &quot;image&quot;: {
                            &quot;uri&quot;: &quot;[parameters('vhdUrl')]&quot;
                        },
                        &quot;vhd&quot;: {
                            &quot;uri&quot;: &quot;[variables('osDiskVhdName')]&quot;
                        },
                        &quot;createOption&quot;: &quot;FromImage&quot;
                    }
                },
                &quot;networkProfile&quot;: {
                    &quot;networkInterfaces&quot;: [
                        {
                            &quot;id&quot;: &quot;[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]&quot;
                        }
                    ]
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines/extensions&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;publisher&quot;: &quot;Microsoft.Compute&quot;,
                &quot;type&quot;: &quot;CustomScriptExtension&quot;,
                &quot;typeHandlerVersion&quot;: &quot;1.4&quot;,
                &quot;settings&quot;: {
                    &quot;fileUris&quot;: [
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd&quot;
                    ],
                    &quot;commandToExecute&quot;: &quot;[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]":::

<br>Hier volgt een voor beeld van test resultaten die worden weer gegeven in een online JSON-Viewer (zoals [code beautify](https://codebeautify.org/jsonviewer) of [JSON Viewer](https://jsonformatter.org/json-viewer)).

![Test resultaten in een online JSON-viewer.](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>KRUL gebruiken om de zelf test-API in Linux-besturings systeem te verbruiken

De API in krul aanroepen:

1. Gebruik de krul opdracht om de API aan te roepen.
2. De methode is post en het inhouds type is JSON, zoals wordt weer gegeven in het volgende code fragment.

```shell
curl POST -H "Content-Type:application/json" -H "Authorization: Bearer XXXXXX-Token-XXXXXXXX"
https://isvapp.azure-api.net/selftest-vm -d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "UserName":"XXX",
"Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD", "AppId":"XXXX-XXXX-XXXX",
"TenantId "XXXX-XXXX-XXXX"}'
```

<br>Hier volgt een voor beeld van het gebruik van krul om de API aan te roepen:

![Voor beeld van het gebruik van krul om de API aan te roepen.](media/vm/use-curl-call-api.png)

<br>Dit is de JSON-resultaten van de krul aanroep:

![De JSON-resultaten van de krul aanroep.](media/vm/test-results-json-viewer-3.png)

## <a name="next-step"></a>Volgende stap

- Lees [veelvoorkomende problemen met SAS-uri's en oplossingen](common-sas-uri-issues.md).

---
title: Geautomatiseerde VM-implementatie met Azure App-configuratie snel gestart
description: Deze quickstart laat zien hoe u de Azure PowerShell-module en Azure Resource Manager-sjablonen gebruiken om een Azure App Configuration Store te implementeren. Gebruik vervolgens de waarden in de winkel om een VM te implementeren.
author: lisaguthrie
ms.author: lcozzens
ms.date: 03/05/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: c45f6855c33dff2790ced306fd7f049b98dd1387
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79126380"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template"></a>Snelstart: geautomatiseerde VM-implementatie met sjabloon App-configuratie en resourcebeheer

De Azure PowerShell-module wordt gebruikt voor het maken en beheren van Azure-resources met behulp van PowerShell-cmdlets of -scripts. In deze snelstart ziet u hoe u Azure PowerShell- en Azure Resource Manager-sjablonen gebruiken om een Azure App Configuration Store te implementeren. Vervolgens leert u hoe u de belangrijkste waarden in de winkel gebruiken om een VM te implementeren.

U gebruikt de vereiste sjabloon om een App Configuration Store te maken en voeg vervolgens belangrijke waarden toe aan de store met behulp van de Azure-portal of Azure CLI. De primaire sjabloon verwijst naar bestaande sleutelwaardeconfiguraties van een bestaand configuratiearchief. De opgehaalde waarden worden gebruikt om eigenschappen in te stellen van de resources die door de sjabloon zijn gemaakt, zoals een VM in dit voorbeeld.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Als u geen Azure-abonnement hebt, maakt u een [gratis account aan.](https://azure.microsoft.com/free/)

* Voor deze quickstart is de Azure PowerShell-module vereist. Voer `Get-Module -ListAvailable Az` uit om de versie op te zoeken die op uw lokale computer is geïnstalleerd. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u met de opdracht `Connect-AzAccount` aan bij uw Azure-abonnement en voer uw Azure-referenties in de pop-upbrowser in:

```azurepowershell-interactive
# Connect to your Azure account
Connect-AzAccount
```

Als u meer dan één abonnement hebt, selecteert u het abonnement dat u wilt gebruiken voor deze quickstart door de volgende cmdlets uit te voeren. Vergeet niet om `<your subscription name>` de naam van uw abonnement te vervangen:

```azurepowershell-interactive
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een Azure-resourcegroep met behulp van de opdracht [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

```azurepowershell-interactive
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="deploy-an-azure-app-configuration-store"></a>Een Azure App Configuration Store implementeren

Voordat u belangrijke waarden op de VM toepassen, moet u een bestaande Azure App Configuration Store hebben. In dit gedeelte wordt beschreven hoe u een Azure App Configuration Store implementeert met behulp van een Azure Resource Manager-sjabloon. Als je al een app config store hebt, kun je naar het volgende gedeelte van dit artikel gaan. 

1. Kopieer en plak de volgende json-code in een nieuw bestand met de naam *prereq.azuredeploy.json*.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "type": "string",
        "metadata": {
          "description": "Specifies the name of the app configuration store."
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Specifies the Azure location where the app configuration store should be created."
        }
      },
      "skuName": {
        "type": "string",
        "defaultValue": "standard",
        "metadata": {
          "description": "Specifies the SKU of the app configuration store."
        }
      }
    },
    "resources": [
      {
        "type": "Microsoft.AppConfiguration/configurationStores",
        "name": "[parameters('configStoreName')]",
        "apiVersion": "2019-10-01",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('skuName')]"
        }
      }
    ]
   }
   ```

1. Kopieer en plak de volgende json-code in een nieuw bestand met de naam *prereq.azuredeploy.parameters.json*. Vervang **GET-UNIQUE** door een unieke naam voor uw Configuratiewinkel.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "value": "GET-UNIQUE"
      }
    }
   }
   ```

1. Voer in het PowerShell-venster de volgende opdracht uit om het Azure App Configuration Store te implementeren. Vergeet niet de naam van de brongroep, het pad met het sjabloonbestand en het bestandspad van de sjabloonparameter te vervangen.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

## <a name="add-vm-configuration-key-values"></a>Vm-configuratiewaarden toevoegen

U een App Configuration Store maken met behulp van een Azure Resource Manager-sjabloon, maar u moet wel sleutelwaarden toevoegen met behulp van de Azure-portal of Azure CLI. In deze quickstart voegt u belangrijke waarden toe met behulp van de Azure-portal.

1. Zodra de implementatie is voltooid, navigeert u naar het nieuw gemaakte App Configuration Store in [Azure portal.](https://portal.azure.com)

1. Selecteer **Toegangstoetsen** > **instellingen**. Noteer de primaire alleen-lezen-toetstekenreeks. U gebruikt deze verbindingstekenreeks later om uw toepassing te configureren om te communiceren met de app-configuratieopslag die u hebt gemaakt.

1. Selecteer **Configuratieverkenner** > **Maken** om de volgende sleutelwaardeparen toe te voegen:

   |Sleutel|Waarde|
   |-|-|
   |windowsOsVersie|Datacenter 2019|
   |diskSizeGB|1023|
  
   Voer *sjabloon* in voor **Label,** maar houd **Inhoudstype** leeg.

## <a name="deploy-vm-using-stored-key-values"></a>VM implementeren met opgeslagen sleutelwaarden

Nu u belangrijke waarden aan de winkel hebt toegevoegd, bent u klaar om een VM te implementeren met behulp van een Azure Resource Manager-sjabloon. De sjabloon verwijst naar de **windowsOsVersion-** en **diskSizeGB-toetsen** die u hebt gemaakt.

1. Kopieer en plak de volgende json-code in een nieuw bestand met de naam *azuredeploy.json*of download het bestand van [Azure Quickstart-sjablonen](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.json).

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "Admin user name."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "appConfigStoreName": {
            "type": "string",
            "metadata": {
                "description": "App configuration store name."
            }
        },
        "appConfigStoreResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Name of the resource group for the app config store."
            }
        },
        "domainNameLabel": {
            "type": "string",
            "metadata": {
                "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_D2_v3",
            "metadata": {
                "description": "Size of the VM"
            }
        },
        "vmSkuKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM windows sku"
            }
        },
        "diskSizeKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM disk size"
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "The name of the storage account."
            }
        }
    },
    "variables": {
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressName": "myPublicIP",
        "vmName": "SimpleWinVM",
        "virtualNetworkName": "MyVNET",
        "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]",
        "appConfigRef": "[resourceId(parameters('appConfigStoreResourceGroup'), 'Microsoft.AppConfiguration/configurationStores', parameters('appConfigStoreName'))]",
        "windowsOSVersionParameters": {
            "key": "[parameters('vmSkuKey')]",
            "label": "template"
        },
        "diskSizeGBParameters": {
            "key": "[parameters('diskSizeKey')]",
            "label": "template"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-11-01",
            "name": "[parameters('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[variables('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('domainNameLabel')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2018-11-01",
            "name": "[variables('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2018-11-01",
            "name": "[variables('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[resourceId('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2018-10-01",
            "name": "[variables('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computerName": "[variables('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('windowsOSVersionParameters')).value]",
                        "version": "latest"
                    },
                    "osDisk": {
                        "createOption": "FromImage"
                    },
                    "dataDisks": [
                        {
                            "diskSizeGB": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('diskSizeGBParameters')).value]",
                            "lun": 0,
                            "createOption": "Empty"
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                        }
                    ]
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))).primaryEndpoints.blob]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "hostname": {
            "type": "string",
            "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]"
        }
    }
   }
   ```

1. Kopieer en plak de volgende json-code in een nieuw bestand met de naam *azuredeploy.parameters.json*of download het bestand van [Azure Quickstart-sjablonen](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.parameters.json).

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminPassword": {
        "value": "GEN-PASSWORD"
      },
      "appConfigStoreName":{
        "value": "GEN-APPCONFIGSTORE-NAME"
      },
      "appConfigStoreResourceGroup": {
         "value": "GEN-APPCONFIGSTORE-RESOURCEGROUP-NAME"
      },
      "vmSkuKey":{
        "value": "GEN-APPCONFIGSTORE-WINDOWSOSVERSION"
      },
      "diskSizeKey" :{
         "value": "GEN-APPCONFIGSTORE-DISKSIZEGB"
      },
      "adminUsername":{
        "value": "GEN-UNIQUE"
      },
      "storageAccountName":{
        "value": "GEN-UNIQUE"
      },
      "domainNameLabel":{
        "value": "GEN-UNIQUE"
      }
    }
   }
   ```

   Vervang de parameterwaarden in de sjabloon door de volgende waarden:

   |Parameter|Waarde|
   |-|-|
   |adminPassword|Een beheerderswachtwoord voor de VM.|
   |appConfigStoreName|De naam van uw Azure App Configuration Store.|
   |appConfigStoreResourceGroup|De brongroep die uw App Configuration Store bevat.|
   |vmSkuKey|*windowsOSVersie*|
   |diskSizeKey|*diskSizeGB*|
   |adminUsername|Een gebruikersgebruikersnaam van de beheerder voor de VM.|
   |storageAccountName|Een unieke naam voor een opslagaccount dat is gekoppeld aan de vm.|
   |domainNameLabel|Een unieke domeinnaam.|

1. Voer in het PowerShell-venster de volgende opdracht uit om het Azure App Configuration Store te implementeren. Vergeet niet de naam van de brongroep, het pad met het sjabloonbestand en het bestandspad van de sjabloonparameter te vervangen.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

Gefeliciteerd! U hebt een VM geïmplementeerd met configuraties die zijn opgeslagen in Azure App-configuratie.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de brongroep, het app-configuratiearchief, de VM en alle gerelateerde bronnen wanneer dit niet meer nodig is. Als u van plan bent om in de toekomst de App Configuration Store of VM te gebruiken, u het verwijderen overslaan. Als u deze taak niet meer gaat gebruiken, verwijdert u alle resources die in deze quickstart zijn gemaakt door de volgende cmdlet uit te voeren:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een VM geïmplementeerd met behulp van een Azure Resource Manager-sjabloon en sleutelwaarden uit Azure App-configuratie.

Ga naar het volgende artikel voor meer informatie over het maken van andere toepassingen met Azure App-configuratie:

> [!div class="nextstepaction"]
> [Snelstart: een ASP.NET Core-app maken met Azure-app-configuratie](quickstart-aspnet-core-app.md)

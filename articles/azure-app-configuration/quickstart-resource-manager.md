---
title: Automatische VM-implementatie met Azure-app configuratie Quick Start
description: In deze Quick start ziet u hoe u de Azure PowerShell-module en Azure Resource Manager sjablonen kunt gebruiken om een Azure-app configuratie archief te implementeren. Gebruik vervolgens de waarden in de Store om een virtuele machine te implementeren.
author: lisaguthrie
ms.author: lcozzens
ms.date: 03/05/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: c45f6855c33dff2790ced306fd7f049b98dd1387
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126380"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template"></a>Snelstartgids: geautomatiseerde VM-implementatie met app-configuratie en Resource Manager-sjabloon

De Azure PowerShell-module wordt gebruikt voor het maken en beheren van Azure-resources met behulp van PowerShell-cmdlets of -scripts. In deze Quick start ziet u hoe u Azure PowerShell en Azure Resource Manager sjablonen kunt gebruiken om een Azure-app configuratie archief te implementeren. Vervolgens leert u hoe u de sleutel waarden in de Store gebruikt om een virtuele machine te implementeren.

U gebruikt de sjabloon vereiste om een app-configuratie archief te maken en vervolgens sleutel waarden toe te voegen aan de Store met behulp van de Azure Portal of Azure CLI. De primaire sjabloon verwijst naar bestaande sleutel-waarde configuraties uit een bestaande configuratie opslag. De opgehaalde waarden worden gebruikt voor het instellen van eigenschappen van de resources die zijn gemaakt door de sjabloon, zoals een virtuele machine in dit voor beeld.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Als u nog geen Azure-abonnement hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan.

* Voor deze quickstart is de Azure PowerShell-module vereist. Voer `Get-Module -ListAvailable Az` uit om de versie op te zoeken die op uw lokale computer is geïnstalleerd. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u met de opdracht `Connect-AzAccount` aan bij uw Azure-abonnement en voer uw Azure-referenties in de pop-upbrowser in:

```azurepowershell-interactive
# Connect to your Azure account
Connect-AzAccount
```

Als u meer dan één abonnement hebt, selecteert u het abonnement dat u voor deze Quick Start wilt gebruiken door de volgende cmdlets uit te voeren. Vergeet niet om `<your subscription name>` te vervangen door de naam van uw abonnement:

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

## <a name="deploy-an-azure-app-configuration-store"></a>Een Azure-app-configuratie archief implementeren

Voordat u sleutel waarden kunt Toep assen op de VM, moet u een bestaande Azure-app configuratie-archief hebben. In deze sectie wordt beschreven hoe u een Azure-app-configuratie archief implementeert met behulp van een Azure Resource Manager sjabloon. Als u al een app-configuratie-archief hebt, kunt u door gaan naar de volgende sectie van dit artikel. 

1. Kopieer de volgende JSON-code en plak deze in een nieuw bestand met de naam *vereisten. azuredeploy. json*.

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

1. Kopieer de volgende JSON-code en plak deze in een nieuw bestand met de naam *vereisten. azuredeploy. para meters. json*. Vervang **Get-Unique** door een unieke naam voor uw configuratie opslag.

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

1. Voer in het Power shell-venster de volgende opdracht uit om de Azure-app configuratie opslag te implementeren. Vergeet niet om de naam van de resource groep, het pad naar het sjabloon bestand en het pad naar de sjabloon parameter bestand te vervangen.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

## <a name="add-vm-configuration-key-values"></a>VM-configuratie sleutel-waarden toevoegen

U kunt een app-configuratie archief maken met behulp van een Azure Resource Manager sjabloon, maar u moet sleutel waarden toevoegen met behulp van de Azure Portal of Azure CLI. In deze Quick Start voegt u sleutel waarden toe met behulp van de Azure Portal.

1. Zodra de implementatie is voltooid, gaat u naar de zojuist gemaakte app-configuratie Store in [Azure Portal](https://portal.azure.com).

1. Selecteer **instellingen** > **toegangs sleutels**. Noteer de primaire alleen-lezen sleutel connection string. U gebruikt deze connection string later om uw toepassing zo te configureren dat deze communiceert met de app-configuratie die u hebt gemaakt.

1. Selecteer configuratie **verkenner** > **maken** om de volgende sleutel-waardeparen toe te voegen:

   |Sleutel|Waarde|
   |-|-|
   |windowsOsVersion|2019-Data Center|
   |diskSizeGB|1023|
  
   Voer de *sjabloon* voor het **Label**in, maar behoud het **inhouds type** leeg.

## <a name="deploy-vm-using-stored-key-values"></a>Een virtuele machine implementeren met behulp van opgeslagen sleutel waarden

Nu u sleutel waarden aan de Store hebt toegevoegd, kunt u een virtuele machine implementeren met behulp van een Azure Resource Manager sjabloon. De sjabloon verwijst naar de sleutels **windowsOsVersion** en **diskSizeGB** die u hebt gemaakt.

1. Kopieer de volgende JSON-code en plak deze in een nieuw bestand met de naam *azuredeploy. json*of down load het bestand vanuit [Azure Quick](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.json)start-sjablonen.

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

1. Kopieer de volgende JSON-code en plak deze in een nieuw bestand met de naam *azuredeploy. para meters. json*of down load het bestand vanuit [Azure Quick](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.parameters.json)start-sjablonen.

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

   Vervang de parameter waarden in de sjabloon door de volgende waarden:

   |Parameter|Waarde|
   |-|-|
   |adminPassword|Een beheerders wachtwoord voor de virtuele machine.|
   |appConfigStoreName|De naam van uw Azure-app-configuratie archief.|
   |appConfigStoreResourceGroup|De resource groep die uw app-configuratie archief bevat.|
   |vmSkuKey|*windowsOSVersion*|
   |diskSizeKey|*diskSizeGB*|
   |adminUsername|Een gebruikers naam voor de beheerder voor de virtuele machine.|
   |storageAccountName|Een unieke naam voor een opslag account dat is gekoppeld aan de virtuele machine.|
   |Domeinnaam label|Een unieke domein naam.|

1. Voer in het Power shell-venster de volgende opdracht uit om de Azure-app configuratie opslag te implementeren. Vergeet niet om de naam van de resource groep, het pad naar het sjabloon bestand en het pad naar de sjabloon parameter bestand te vervangen.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

Gefeliciteerd. U hebt een virtuele machine geïmplementeerd met configuraties die zijn opgeslagen in Azure-app configuratie.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resource groep, de app-configuratie opslag, de VM en alle gerelateerde resources. Als u van plan bent om het app-configuratie archief of de virtuele machine in de toekomst te gebruiken, kunt u het verwijderen overs Laan. Als u deze taak niet meer gaat gebruiken, verwijdert u alle resources die in deze quickstart zijn gemaakt door de volgende cmdlet uit te voeren:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een virtuele machine geïmplementeerd met behulp van een Azure Resource Manager sjabloon en de sleutel waarden van Azure-app configuratie.

Ga door naar het volgende artikel voor meer informatie over het maken van andere toepassingen met Azure-app configuratie:

> [!div class="nextstepaction"]
> [Snelstartgids: een ASP.NET Core-app maken met Azure-app configuratie](quickstart-aspnet-core-app.md)

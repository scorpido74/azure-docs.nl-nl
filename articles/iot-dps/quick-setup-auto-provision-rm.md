---
title: Set up Azure IoT Hub Device Provisioning using Azure Resource Manager template
description: Azure quickstart - Set up the Azure IoT Hub Device Provisioning Service using a template
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 9a763a492496cef0f01468dc4b655a61e91a2a53
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229475"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-an-azure-resource-manager-template"></a>Quickstart: Set up the IoT Hub Device Provisioning Service with an Azure Resource Manager template

U kunt [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) gebruiken voor het programmatisch instellen van de benodigde Azure-cloudresources voor het inrichten van uw apparaten. These steps show how to create an IoT hub and a new IoT Hub Device Provisioning Service, and link the two services together using an Azure Resource Manager template. This quickstart uses [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) to perform the programmatic steps necessary to create a resource group and deploy the template, but you can easily use the [Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), .NET, Ruby, or other programming languages to perform these steps and deploy your template. 


## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- This quickstart requires that you run the Azure CLI locally. Azure CLI versie 2.0 of hoger moet geïnstalleerd zijn. Voer `az --version` uit om de versie te bekijken. Als u de CLI wilt installeren of upgraden, raadpleegt u [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli).


## <a name="sign-in-to-azure-and-create-a-resource-group"></a>Aanmelden bij Azure en een resourcegroep maken

Meld u aan bij uw Azure-account en selecteer uw abonnement.

1. At the command prompt, run the [login command][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Volg de instructies om te verifiëren met de code en meld u aan bij uw Azure-account via een webbrowser.

2. Als u meerdere Azure-abonnementen hebt en u zich aanmeldt bij Azure, hebt u toegang tot alle Azure accounts die zijn gekoppeld aan uw referenties. Use the following [command to list the Azure accounts][lnk-az-account-command] available for you to use:
    
    ```azurecli
    az account list 
    ```

    Gebruik de volgende opdracht om het abonnement te selecteren dat u wilt gebruiken voor het uitvoeren van de opdrachten voor het maken van uw IoT-hub. U kunt de naam van het abonnement of de id van de uitvoer van de vorige opdracht gebruiken:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Wanneer u Azure-cloudresources zoals IoT-hubs maakt en services inricht, groepeert u deze in een resourcegroep. Either use an existing resource group, or run the following [command to create a resource group][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > In het voorbeeld wordt de resourcegroep gemaakt in de locatie US - west. U kunt een lijst met beschikbare locaties weergeven met de opdracht `az account list-locations -o table`.
    >
    >

## <a name="create-a-resource-manager-template"></a>Een Resource Manager-sjabloon maken

Gebruik een JSON-sjabloon voor het maken van een inrichtingsservice en een gekoppelde IoT-hub in de resourcegroep. U kunt ook een Azure Resource Manager-sjabloon gebruiken om een bestaande inrichtingsservice of IoT-hub te wijzigen.

1. Gebruik een teksteditor voor het maken van een Azure Resource Manager-sjabloon met de naam **template.json** met de volgende minimale inhoud. 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": []
   }
   ```

2. Vervang de sectie **parameters** door de volgende inhoud. De sectie parameters bevat de parameters die kunnen worden doorgegeven vanuit een ander bestand. In deze sectie wordt de naam van de IoT-hub en de inrichtingsservice die u wilt maken opgegeven. Ook de locatie voor de IoT-hub en de locatie voor de inrichtingsservice worden hierin opgegeven. De waarden zijn beperkt tot Azure-regio's die ondersteuning bieden voor IoT-hubs en inrichtingsservices. Voer de opdracht `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` uit voor een lijst van ondersteunde locaties voor Device Provisioning Service, of ga naar de pagina [Azure Status](https://azure.microsoft.com/status/) en zoek naar 'Device Provisioning Service'.

   ```json
    "parameters": {
        "iotHubName": {
            "type": "string"
        },
        "provisioningServiceName": {
            "type": "string"
        },
        "hubLocation": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "westus",
                "westeurope",
                "northeurope",
                "southeastasia",
                "eastasia"
            ]
        }
    },

   ```

3. Vervang de sectie **variables** (variabelen) door de volgende inhoud. In deze sectie definieert u waarden die later worden gebruikt voor het opbouwen van de verbindingsreeks voor de IoT-hub, die nodig is om de inrichtingsservice aan de IoT-hub te koppelen. 
 
   ```json
    "variables": {        
        "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
        "iotHubKeyName": "iothubowner",
        "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
    },

   ```

4. Als u een IoT-hub wilt maken, voegt u de volgende regels toe aan de verzameling **resources**. The JSON specifies the minimum properties required to create an IoT hub. De eigenschappen **name** (naam) en **location** (locatie) worden als parameters doorgegeven. To learn more about the properties you can specify for an IoT hub in a template, see [Microsoft.Devices/IotHubs template reference](https://docs.microsoft.com/azure/templates/microsoft.devices/iothubs).

   ```json
        {
            "apiVersion": "2017-07-01",
            "type": "Microsoft.Devices/IotHubs",
            "name": "[parameters('iotHubName')]",
            "location": "[parameters('hubLocation')]",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "tags": {
            },
            "properties": {
            }            
        },

   ``` 

5. Voor het maken van de inrichtingsservice voegt u de volgende regels toe na de specificatie van de IoT-hub in de verzameling **resources**. De **naam** en **locatie** van de inrichtingsservice worden doorgegeven in parameters. Geef de IoT-hubs waaraan u de inrichtingsservice wilt koppelen op in de verzameling **iotHubs**. U moet ten minste de eigenschappen **connectionString** en **location** opgeven voor elke gekoppelde IoT-hub. U kunt ook eigenschappen zoals **allocationWeight** en **applyAllocationPolicy** instellen voor elke IoT-hub, evenals de eigenschappen **allocationPolicy** en  **authorizationPolicies** voor de inrichtingsservice zelf. Zie [Microsoft.Devices/provisioningServices template reference](https://docs.microsoft.com/azure/templates/microsoft.devices/provisioningservices) voor meer informatie.

   De eigenschap **dependsOn** wordt gebruikt om ervoor te zorgen dat Resource Manager de IoT-hub vóór de inrichtingsservice maakt. Voor de sjabloon is de verbindingsreeks van de IoT-hub vereist voordat de koppeling met de inrichtingsservice kan worden opgegeven, zodat de hub en de sleutels eerst moeten worden gemaakt. De sjabloon maakt gebruik van functies zoals **concat** en **listKeys** voor het maken van de verbindingsreeks. Zie [Functies van Azure Resource Manager-sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions) voor meer informatie.

   ```json
        {
            "type": "Microsoft.Devices/provisioningServices",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "name": "[parameters('provisioningServiceName')]",
            "apiVersion": "2017-11-15",
            "location": "[parameters('hubLocation')]",
            "tags": {},
            "properties": {
                "iotHubs": [
                    {
                        "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                        "location": "[parameters('hubLocation')]",
                        "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                    }
                ]
            },
            "dependsOn": ["[parameters('iotHubName')]"]
        }

   ```

6. Sla het sjabloonbestand op. De voltooide sjabloon moet er als volgt uitzien:

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "iotHubName": {
               "type": "string"
           },
           "provisioningServiceName": {
               "type": "string"
           },
           "hubLocation": {
               "type": "string",
               "allowedValues": [
                   "eastus",
                   "westus",
                   "westeurope",
                   "northeurope",
                   "southeastasia",
                   "eastasia"
               ]
           }
       },
       "variables": {        
           "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
           "iotHubKeyName": "iothubowner",
           "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
       },
       "resources": [
           {
               "apiVersion": "2017-07-01",
               "type": "Microsoft.Devices/IotHubs",
               "name": "[parameters('iotHubName')]",
               "location": "[parameters('hubLocation')]",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "tags": {
               },
               "properties": {
               }            
           },
           {
               "type": "Microsoft.Devices/provisioningServices",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "name": "[parameters('provisioningServiceName')]",
               "apiVersion": "2017-11-15",
               "location": "[parameters('hubLocation')]",
               "tags": {},
               "properties": {
                   "iotHubs": [
                       {
                           "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                           "location": "[parameters('hubLocation')]",
                           "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                       }
                   ]
               },
               "dependsOn": ["[parameters('iotHubName')]"]
           }
       ]
   }
   ```

## <a name="create-a-resource-manager-parameter-file"></a>Een Resource Manager-parameterbestand maken

The template that you defined in the last step uses parameters to specify the name of the IoT hub, the name of the provisioning service, and the location (Azure region) to create them. U geeft deze parameters door in een afzonderlijk bestand. Zo kunt u dezelfde sjabloon voor meerdere implementaties gebruiken. Volg deze stappen voor het maken van het parameterbestand:

1. Gebruik een teksteditor voor het maken van een Azure Resource Manager-parameterbestand met de naam **parameters.json** met de volgende minimale inhoud: 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {}
       }
   }
   ```

2. Voeg de waarde voor **iotHubName** toe aan de sectie parameters.  An IoT hub name must be globally unique in Azure, so you may want to add a unique prefix or suffix to the example name, or choose a new name altogether. Make sure your name follows proper naming conventions for an IoT hub: it should be 3-50 characters in length, and can contain only upper or lower case alphanumeric characters or hyphens ('-'). 

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
    }
   
   ```

3. Voeg de waarde voor **provisioningServiceName** toe aan de sectie parameters. You will also need to choose a globally unique name for your provisioning service. Make sure it follows proper naming conventions for an IoT Hub Device Provisioning Service: it should be 3-64 characters in length and can contain only upper or lower case alphanumeric characters or hyphens ('-').

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
    }

   ```

4. Voeg de waarde voor **hubLocation** toe aan de sectie parameters. Hiermee wordt zowel de locatie voor de IoT-hub als de locatie voor de inrichtingsservice opgegeven. De waarde moet overeenkomen met een van de opgegeven locaties in de verzameling **allowedValues** in de parameterdefinitie in het sjabloonbestand. Deze verzameling beperkt de waarden tot Azure-locaties die ondersteuning bieden voor zowel IoT-hubs als inrichtingsservices. Voer de opdracht `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` uit voor een lijst van ondersteunde locaties voor Device Provisioning Service, of ga naar de pagina [Azure Status](https://azure.microsoft.com/status/) en zoek naar 'Device Provisioning Service'.

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
        "hubLocation": {
            "value": "westus"
        }
    }

   ```

5. Sla het bestand op. 


> [!IMPORTANT]
> De IoT-hub en de inrichtingsservice kunnen openbaar worden gedetecteerd als DNS-eindpunten. Zorg er dus voor dat wordt voorkomen dat gevoelige informatie in de namen wordt vermeld.
>

## <a name="deploy-the-template"></a>De sjabloon implementeren

Met de volgende Azure CLI-opdrachten kunt u de sjablonen implementeren en de implementatie controleren.

1. Voer de volgende [opdracht voor het starten van een implementatie](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create) uit om uw sjabloon te implementeren:
    
    ```azurecli
     az group deployment create -g {your resource group name} --template-file template.json --parameters @parameters.json
    ```

   Zoek de eigenschap **provisioningState** die is ingesteld op Succeeded (Geslaagd) in de uitvoer. 

   ![Uitvoer van inrichting](./media/quick-setup-auto-provision-rm/output.png) 


2. Voer de volgende [opdracht voor het weergeven van resources](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-list) uit en zoek de nieuwe inrichtingsservice en IoT-hub in de uitvoer om de implementatie te controleren:

    ```azurecli
     az resource list -g {your resource group name}
    ```


## <a name="clean-up-resources"></a>Resources opschonen

Andere Quick Starts in deze verzameling zijn op deze Quick Start gebaseerd. Als u van plan bent om door te gaan met andere Quick Starts of met de zelfstudies, verwijdert u de resources die u in deze Quick Start hebt gemaakt niet. If you do not plan to continue, you can use the Azure CLI to [delete an individual resource][lnk-az-resource-command], such as an IoT hub or a provisioning service, or to delete a resource group and all of its resources.

Voer de volgende opdracht uit voor het verwijderen van een inrichtingsservice:

```azurecli
az iot hub delete --name {your provisioning service name} --resource-group {your resource group name}
```
Voer de volgende opdracht uit voor het verwijderen van een IoT-hub:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Voer de volgende opdracht uit voor het verwijderen van een resourcegroep en alle bijbehorende resources:

```azurecli
az group delete --name {your resource group name}
```

U kunt ook resourcegroepen en afzonderlijke resources verwijderen met behulp van Azure Portal, PowerShell of REST-API's of ondersteunde platform-SDK's die zijn gepubliceerd voor Azure Resource Manager of de IoT Hub Device Provisioning-service.

## <a name="next-steps"></a>Volgende stappen

In this quickstart, you’ve deployed an IoT hub and a Device Provisioning Service instance, and linked the two resources. To learn how to use this setup to provision a simulated device, continue to the quickstart for creating a simulated device.

> [!div class="nextstepaction"]
> [Quickstart to create a simulated device](./quick-create-simulated-device.md)


<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 

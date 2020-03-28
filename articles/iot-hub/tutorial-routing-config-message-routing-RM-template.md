---
title: Berichtroutering configureren voor Azure IoT Hub met een Azure Resource Manager-sjabloon
description: Berichtroutering configureren voor Azure IoT Hub met een Azure Resource Manager-sjabloon
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 8f245653a8b84944e1e8a3f48a49992f0065be58
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084391"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Zelfstudie: Een Azure Resource Manager-sjabloon gebruiken om de routeringsroute van iot-hubberichten te configureren

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Berichtroutering

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>Het sjabloon- en parametersbestand downloaden

Voor het tweede deel van deze zelfstudie download t u een Visual Studio-toepassing om berichten naar de IoT-hub te verzenden. Er is een map in die download die de Azure Resource Manager-sjabloon en parametersbestand bevat, evenals de Azure CLI- en PowerShell-scripts.

Download nu de [Azure IoT C#Samples.](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) Rits het bestand master.zip uit. De sjabloon Resourcebeheer en het parametersbestand bevinden zich in /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ as **template_iothub.json** en **template_iothub_parameters.json**.

## <a name="create-your-resources"></a>Uw resources maken

U gaat een RM-sjabloon (Azure Resource Manager) gebruiken om al uw resources te maken. De Azure CLI- en PowerShell-scripts kunnen een paar regels tegelijk worden uitgevoerd. Een RM-sjabloon wordt in één stap geïmplementeerd. In dit artikel ziet u de secties afzonderlijk om u te helpen ze allemaal te begrijpen. Vervolgens wordt u weergegeven hoe u de sjabloon implementeert en maakt u het virtuele apparaat voor tests. Nadat de sjabloon is geïmplementeerd, u de configuratie van de berichtroutering in de portal bekijken.

Er zijn verschillende resourcenamen die wereldwijd uniek moeten zijn, zoals de naam IoT Hub en de naam van het opslagaccount. Om de naamgeving van de resources eenvoudiger te maken, worden deze resourcenamen ingesteld om een willekeurige alfanumerieke waarde toe te toevoegen die is gegenereerd op basis van de huidige datum/tijd. 

Als u de sjabloon bekijkt, ziet u waar variabelen zijn ingesteld voor deze resources die de parameter die is doorgegeven en *randomValue* toevoegen aan de parameter. 

In de volgende sectie worden de gebruikte parameters uitgelegd.

### <a name="parameters"></a>Parameters

De meeste van deze parameters hebben standaardwaarden. Degenen die eindigen met **_in** worden gekoppeld met *randomValue* om ze wereldwijd uniek te maken. 

**randomValue:** Deze waarde wordt gegenereerd vanaf de huidige datum/tijd waarop u de sjabloon implementeert. Dit veld bevindt zich niet in het parametersbestand, omdat het wordt gegenereerd in de sjabloon zelf.

**abonnementId**: Dit veld is ingesteld op het abonnement waarin u de sjabloon implementeert. Dit veld bevindt zich niet in het parametersbestand omdat het voor u is ingesteld.

**IoTHubName_in**: Dit veld is de naam van de BasisIoT Hub, die is gekoppeld aan de randomValue om wereldwijd uniek te zijn.

**locatie:** dit veld is het Azure-gebied waarin u implementeert, zoals 'westus'.

**consumer_group**: Dit veld is de consumentengroep die is ingesteld voor berichten die door het eindpunt van de routering komen. Het wordt gebruikt om resultaten te filteren in Azure Stream Analytics. Er is bijvoorbeeld de hele stream waar je alles krijgt, of als je gegevens hebt die binnenkomen met consumer_group ingesteld op **Contoso,** dan kun je een Azure Stream Analytics-stream (en Power BI-rapport) instellen om alleen die items weer te geven. Dit veld wordt gebruikt in deel 2 van deze tutorial.

**sku_name**: Dit veld is de schaling voor de IoT-hub. Deze waarde moet S1 of hoger zijn; een gratis laag werkt niet voor deze zelfstudie omdat het niet meerdere eindpunten toestaat.

**sku_units**: Dit veld gaat mee met de **sku_name**en is het aantal IoT Hub-eenheden dat kan worden gebruikt.

**d2c_partitions:** dit veld is het aantal partities dat wordt gebruikt voor de gebeurtenisstream.

**storageAccountName_in:** dit veld is de naam van het opslagaccount dat moet worden gemaakt. Berichten worden doorgestuurd naar een container in het opslagaccount. Dit veld is gekoppeld aan de randomValue om het wereldwijd uniek te maken.

**storageContainerName:** Dit veld is de naam van de container waarin de berichten worden doorgestuurd naar het opslagaccount worden opgeslagen.

**storage_endpoint:** dit veld is de naam voor het eindpunt van het opslagaccount dat wordt gebruikt door de berichtroutering.

**service_bus_namespace_in:** Dit veld is de naam van de naamruimte servicebus die moet worden gemaakt. Deze waarde wordt gekoppeld aan de randomValue om het wereldwijd uniek te maken.

**service_bus_queue_in:** dit veld is de naam van de wachtrij servicebus die wordt gebruikt voor het routeren van berichten. Deze waarde wordt gekoppeld aan de randomValue om het wereldwijd uniek te maken.

**AuthRules_sb_queue**: Dit veld is de autorisatieregels voor de wachtrij voor de servicebus die wordt gebruikt om de verbindingstekenreeks voor de wachtrij op te halen.

### <a name="variables"></a>Variabelen

Deze waarden worden gebruikt in de sjabloon en zijn meestal afgeleid van parameters.

**queueAuthorizationRuleResourceId**: Dit veld is de ResourceId voor de autorisatieregel voor de wachtrij servicebus. ResourceId wordt op zijn beurt gebruikt om de verbindingstekenreeks voor de wachtrij op te halen.

**iotHubName**: Dit veld is de naam van de IoT-hub nadat de waarde willekeurig is samengevoegd. 

**storageAccountName:** Dit veld is de naam van de opslagaccount nadat randomValue is samengevoegd. 

**service_bus_namespace**: Dit veld is de naamruimte na het hebben van randomValue concatenated.

**service_bus_queue**: Dit veld is de naam van de wachtrij servicebus nadat u willekeurig waarde hebt geconcateneerd.

**sbVersion**: THe versie van de Service Bus API te gebruiken. In dit geval is het "2017-04-01".

### <a name="resources-storage-account-and-container"></a>Resources: Opslagaccount en container

De eerste bron die is gemaakt, is het opslagaccount, samen met de container waarnaar berichten worden doorgestuurd. De container is een resource onder het opslagaccount. Het heeft `dependsOn` een clausule voor de opslagrekening, die de opslagrekening vereist vóór de container worden gemaakt.

Zo ziet deze sectie eruit:

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "properties": {},
    "resources": [
        {
        "type": "blobServices/containers",
        "apiVersion": "2018-07-01",
        "name": "[concat('default/', parameters('storageContainerName'))]",
        "properties": {
            "publicAccess": "None"
            } ,
        "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
            ]
        }
    ]
}
```

### <a name="resources-service-bus-namespace-and-queue"></a>Resources: naamruimte en wachtrij servicebus

De tweede bron die is gemaakt, is de naamruimte servicebus, samen met de wachtrij servicebus naar welke berichten worden doorgestuurd. De SKU is ingesteld op standaard. De API-versie wordt opgehaald uit de variabelen. Het is ook ingesteld om de naamruimte van de servicebus te activeren wanneer deze sectie wordt geïmplementeerd (status:Actief). 

```json
{
    "type": "Microsoft.ServiceBus/namespaces",
    "comments": "The Sku should be 'Standard' for this tutorial.",
    "sku": {
        "name": "Standard",
        "tier": "Standard"
    },
    "name": "[variables('service_bus_namespace')]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "properties": {
        "provisioningState": "Succeeded",
        "metricId": "[concat('a4295411-5eff-4f81-b77e-276ab1ccda12:', variables('service_bus_namespace'))]",
        "serviceBusEndpoint": "[concat('https://', variables('service_bus_namespace'),'.servicebus.windows.net:443/')]",
        "status": "Active"
    },
    "dependsOn": []
}
```

In deze sectie wordt de wachtrij servicebus gesmaakt. Dit deel van het `dependsOn` script heeft een clausule die ervoor zorgt dat de naamruimte wordt gemaakt vóór de wachtrij.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {},
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]"
    ]
}
```

### <a name="resources-iot-hub-and-message-routing"></a>Bronnen: iot-hub en berichtroutering

Nu het opslagaccount en de wachtrij voor servicebus zijn gemaakt, maakt u de IoT-hub waarmee berichten naar hen worden verzonden. De RM-sjabloon gebruikt `dependsOn` clausules, zodat deze niet probeert de hub te maken voordat de Service Bus-bronnen en het opslagaccount zijn gemaakt. 

Hier is het eerste deel van de IoT Hub sectie. Dit deel van de sjabloon stelt de afhankelijkheden in en begint met de eigenschappen.

```json
{
    "apiVersion": "2018-04-01",
    "type": "Microsoft.Devices/IotHubs",
    "name": "[variables('IoTHubName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ],
    "properties": {
        "eventHubEndpoints": {}
            "events": {
                "retentionTimeInDays": 1,
                "partitionCount": "[parameters('d2c_partitions')]"
                }
            },
```

De volgende sectie is de sectie voor de configuratie van de berichtroutering voor de Iot Hub. De eerste is de sectie voor de eindpunten. In dit deel van de sjabloon worden de routeringseindpunten voor de wachtrij servicebus en het opslagaccount ingesteld, inclusief de verbindingstekenreeksen.

Als u de verbindingstekenreeks voor de wachtrij wilt maken, hebt u de wachtrijAutorisatieResourcesResourcedId nodig, die inline wordt opgehaald. Als u de verbindingstekenreeks voor het opslagaccount wilt maken, haalt u de primaire opslagsleutel op en gebruikt u deze vervolgens in de indeling voor de verbindingstekenreeks.

De eindpuntconfiguratie is ook de plaats `AVRO` waar `JSON`u de blob-indeling instelt op of .

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

 ```json
"routing": {
    "endpoints": {
        "serviceBusQueues": [
        {
            "connectionString": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]",
            "name": "[parameters('service_bus_queue_endpoint')]",
            "subscriptionId": "[parameters('subscriptionId')]", 
            "resourceGroup": "[resourceGroup().Name]"
        }
        ],
        "serviceBusTopics": [],
        "eventHubs": [],
        "storageContainers": [
            {
                "connectionString": 
                "[Concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]",
                "containerName": "[parameters('storageContainerName')]",
                "fileNameFormat": "{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}",
                "batchFrequencyInSeconds": 100,
                "maxChunkSizeInBytes": 104857600,
                "encoding": "avro",
                "name": "[parameters('storage_endpoint')]",
                "subscriptionId": "[parameters('subscriptionId')]",
                "resourceGroup": "[resourceGroup().Name]"
            }
        ]
    },
```

Deze volgende sectie is voor de berichtroutes naar de eindpunten. Er is één ingesteld voor elk eindpunt, dus er is er een voor de wachtrij servicebus en één voor de opslagaccountcontainer.

Houd er rekening mee dat de queryvoorwaarde `level="storage"`voor de berichten die naar de opslag worden doorgestuurd, `level="critical"`is en dat de queryvoorwaarde voor de berichten die worden doorgestuurd naar de wachtrij servicebus is .

```json
"routes": [
    {
        "name": "contosoStorageRoute",
        "source": "DeviceMessages",
        "condition": "level=\"storage\"",
        "endpointNames": [
            "[parameters('storage_endpoint')]"
            ],
        "isEnabled": true
    },
    {
        "name": "contosoSBQueueRoute",
        "source": "DeviceMessages",
        "condition": "level=\"critical\"",
        "endpointNames": [
            "[parameters('service_bus_queue_endpoint')]"
            ],
        "isEnabled": true
    }
],
```

Deze json toont de rest van de IoT Hub-sectie, die standaardinformatie en de SKU voor de hub bevat.

```json
            "fallbackRoute": {
                "name": "$fallback",
                "source": "DeviceMessages",
                "condition": "true",
                "endpointNames": [
                    "events"
                ],
                "isEnabled": true
            }
        },
        "storageEndpoints": {
            "$default": {
                "sasTtlAsIso8601": "PT1H",
                "connectionString": "",
                "containerName": ""
            }
        },
        "messagingEndpoints": {
            "fileNotifications": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        },
        "enableFileUploadNotifications": false,
        "cloudToDevice": {
            "maxDeliveryCount": 10,
            "defaultTtlAsIso8601": "PT1H",
            "feedback": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        }
    },
    "sku": {
        "name": "[parameters('sku_name')]",
        "capacity": "[parameters('sku_units')]"
    }
}
```

### <a name="resources-service-bus-queue-authorization-rules"></a>Resources: autorisatieregels voor wachtrijen servicebus

De autorisatieregel voor wachtrijwachtrijen servicebus wordt gebruikt om de verbindingstekenreeks voor de wachtrij servicebus op te halen. Het maakt `dependsOn` gebruik van een clausule om ervoor te zorgen dat deze niet wordt gemaakt voordat de servicebusnaamruimte en de wachtrij voor servicebus worden gebruikt.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues/authorizationRules",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'), '/', parameters('AuthRules_sb_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {
        "rights": [
            "Send"
        ]
    },
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ]
},
```

### <a name="resources-consumer-group"></a>Bronnen: Consumentengroep

In deze sectie maakt u een consumentengroep voor de IoT Hub-gegevens die door de Azure Stream Analytics in het tweede deel van deze zelfstudie moeten worden gebruikt.

```json
{
    "type": "Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups",
    "name": "[concat(variables('iotHubName'), '/events/',parameters('consumer_group'))]",
    "apiVersion": "2018-04-01",
    "dependsOn": [
        "[concat('Microsoft.Devices/IotHubs/', variables('iotHubName'))]"
    ]
}
```

### <a name="resources-outputs"></a>Resources: uitgangen

Als u een waarde terug wilt sturen naar het te weergeven implementatiescript, gebruikt u een uitvoersectie. Dit deel van de sjabloon retourneert de verbindingstekenreeks voor de wachtrij servicebus. Het retourneren van een waarde is niet vereist, het is opgenomen als een voorbeeld van hoe u resultaten retourneren naar het aanroepende script.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>De RM-sjabloon implementeren

Als u de sjabloon wilt implementeren in Azure, uploadt u de sjabloon en het parametersbestand naar Azure Cloud Shell en voert u vervolgens een script uit om de sjabloon te implementeren. Open Azure Cloud Shell en meld u aan. In dit voorbeeld wordt PowerShell gebruikt.

Als u de bestanden wilt uploaden, selecteert u het pictogram **Bestanden uploaden/downloaden** in de menubalk en kiest u Uploaden.

![Cloud Shell-menubalk met bestanden uploaden/downloaden gemarkeerd](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Gebruik de Verkenner die verschijnt om de bestanden op uw lokale schijf te vinden en deze te selecteren en kies **Openen**.

Nadat de bestanden zijn geüpload, wordt in een dialoogvenster met resultaten iets weergegeven als de volgende afbeelding.

![Cloud Shell-menubalk met bestanden uploaden/downloaden gemarkeerd](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

De bestanden worden geüpload naar het aandeel dat wordt gebruikt door uw Cloud Shell-exemplaar. 

Voer het script uit om de implementatie uit te voeren. De laatste regel van dit script haalt de variabele op die is ingesteld om te worden geretourneerd: de verbindingstekenreeks servicebuswachtrij.

Het script stelt en gebruikt deze variabelen:

**$RGName** is de naam van de resourcegroep om de sjabloon te implementeren. Dit veld wordt gemaakt voordat de sjabloon wordt geïmplementeerd.

**$location** is de Azure-locatie die moet worden gebruikt voor de sjabloon, zoals 'westus'.

**implementatienaam** is een naam die u aan de implementatie toewijst om de terugkerende variabele waarde op te halen.

Hier is het PowerShell script. Kopieer dit PowerShell-script en plak het in het venster Cloud Shell en druk vervolgens op Enter om het uit te voeren.

```powershell
$RGName="ContosoResources"
$location = "westus"
$deploymentname="contoso-routing"

# Remove the resource group if it already exists. 
#Remove-AzResourceGroup -name $RGName 
# Create the resource group.
New-AzResourceGroup -name $RGName -Location $location 

# Set a path to the parameter file. 
$parameterFile = "$HOME/template_iothub_parameters.json"
$templateFile = "$HOME/template_iothub.json"

# Deploy the template.
New-AzResourceGroupDeployment `
    -Name $deploymentname `
    -ResourceGroupName $RGName `
    -TemplateParameterFile $parameterFile `
    -TemplateFile $templateFile `
    -verbose

# Get the returning value of the connection string.
(Get-AzResourceGroupDeployment -ResourceGroupName $RGName -Name $deploymentname).Outputs.sbq_connectionString.value
```

Als u scriptfouten hebt, u het script lokaal bewerken, opnieuw uploaden naar de Cloud Shell en het script opnieuw uitvoeren. Nadat het script is voltooid, gaat u verder met de volgende stap.

## <a name="create-simulated-device"></a>Een gesimuleerd apparaat maken

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Berichtroutering weergeven in de portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u alle resources hebt ingesteld en de berichtroutes zijn geconfigureerd, gaat u door naar de volgende zelfstudie om te leren hoe u de informatie over de omgeleide berichten verwerken en weergeven.

> [!div class="nextstepaction"]
> [Deel 2 - Bekijk de resultaten van de routeringsresultaten van het bericht](tutorial-routing-view-message-routing-results.md)

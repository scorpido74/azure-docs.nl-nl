---
title: Bericht routering voor Azure IoT Hub configureren met een Azure Resource Manager sjabloon
description: Bericht routering voor Azure IoT Hub configureren met een Azure Resource Manager sjabloon
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 8f245653a8b84944e1e8a3f48a49992f0065be58
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74084391"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Zelf studie: een Azure Resource Manager sjabloon gebruiken om IoT Hub bericht routering te configureren

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Berichtroutering

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>De sjabloon en het parameter bestand downloaden

Voor het tweede deel van deze zelf studie downloadt en voert u een Visual Studio-toepassing uit om berichten naar de IoT Hub te verzenden. Er is een map in die down load die de Azure Resource Manager sjabloon en het parameter bestand bevat, evenals de Azure CLI-en Power shell-scripts.

Ga nu verder met het downloaden van [Azure IOT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) -voor beelden. Pak het zip-bestand master. De Resource Manager-sjabloon en het parameter bestand bevinden zich in/iot-hub/Tutorials/Routing/SimulatedDevice/resources/als **template_iothub. json** en **template_iothub_parameters. json**.

## <a name="create-your-resources"></a>Uw resources maken

U gaat een Azure Resource Manager RM-sjabloon gebruiken om al uw resources te maken. De Azure CLI-en Power shell-scripts kunnen een aantal regels tegelijk uitvoeren. Een RM-sjabloon wordt in één stap geïmplementeerd. In dit artikel wordt beschreven hoe u de secties afzonderlijk kunt begrijpen. Vervolgens wordt uitgelegd hoe u de sjabloon implementeert en maakt u het virtuele apparaat om te testen. Nadat de sjabloon is geïmplementeerd, kunt u de configuratie voor bericht routering bekijken in de portal.

Er zijn verschillende resource namen die wereld wijd uniek moeten zijn, zoals de naam van de IoT Hub en de naam van het opslag account. Om de naam van de resources gemakkelijker te maken, worden deze resource namen ingesteld om een wille keurige alfanumerieke waarde toe te voegen die is gegenereerd op basis van de huidige datum/tijd. 

Als u de sjabloon bekijkt, ziet u dat er variabelen worden ingesteld voor deze resources waarvoor de para meter wordt door gegeven en wordt *randomValue* samengevoegd met de para meter. 

In de volgende sectie worden de gebruikte para meters uitgelegd.

### <a name="parameters"></a>Parameters

De meeste van deze para meters hebben standaard waarden. De taken die eindigen met **_in** worden samengevoegd met *randomValue* om ze globaal uniek te maken. 

**randomValue**: deze waarde wordt gegenereerd op basis van de huidige datum/tijd wanneer u de sjabloon implementeert. Dit veld bevindt zich niet in het parameter bestand, omdat het in de sjabloon zelf wordt gegenereerd.

**subscriptionId**: dit veld wordt voor u ingesteld op het abonnement waarin u de sjabloon implementeert. Dit veld bevindt zich niet in het parameter bestand omdat het voor u is ingesteld.

**IoTHubName_in**: dit veld is de naam van de basis IOT hub die wordt samengevoegd met de randomValue die wereld wijd uniek moet zijn.

**locatie**: dit veld is de Azure-regio waarin u implementeert, zoals ' westelijke '.

**consumer_group**: dit veld is de gebruikers groep die is ingesteld voor berichten die worden verzonden via het eind punt van de route ring. Het wordt gebruikt voor het filteren van resultaten in Azure Stream Analytics. Zo is de hele stroom waar u alles krijgt, of als u gegevens hebt ontvangen via consumer_group is ingesteld op **Contoso**, kunt u een Azure stream Analytics stroom (en Power bi rapport) zo instellen dat alleen die vermeldingen worden weer gegeven. Dit veld wordt gebruikt in deel 2 van deze zelf studie.

**sku_name**: dit veld is het formaat van de IOT hub. Deze waarde moet S1 of hoger zijn. een gratis laag werkt niet voor deze zelf studie omdat er geen meerdere eind punten zijn toegestaan.

**sku_units**: dit veld bevat de **sku_name**en het aantal IOT hub eenheden dat kan worden gebruikt.

**d2c_partitions**: dit veld is het aantal partities dat wordt gebruikt voor de gebeurtenis stroom.

**storageAccountName_in**: dit veld is de naam van het opslag account dat moet worden gemaakt. Berichten worden doorgestuurd naar een container in het opslag account. Dit veld wordt samengevoegd met het randomValue om het globaal uniek te maken.

**storageContainerName**: dit veld bevat de naam van de container waarin de berichten worden opgeslagen die naar het opslag account worden doorgestuurd.

**storage_endpoint**: dit veld is de naam voor het eind punt van het opslag account dat wordt gebruikt door de bericht routering.

**service_bus_namespace_in**: dit veld is de naam van de service bus naam ruimte die u wilt maken. Deze waarde wordt samengevoegd met de randomValue om deze wereld wijd uniek te maken.

**service_bus_queue_in**: dit veld is de naam van de service bus wachtrij die wordt gebruikt voor de route ring van berichten. Deze waarde wordt samengevoegd met de randomValue om deze wereld wijd uniek te maken.

**AuthRules_sb_queue**: dit veld bevat de autorisatie regels voor de service bus-wachtrij, die wordt gebruikt voor het ophalen van de Connection String voor de wachtrij.

### <a name="variables"></a>Variabelen

Deze waarden worden gebruikt in de sjabloon en worden meestal afgeleid van para meters.

**queueAuthorizationRuleResourceId**: dit veld is het ResourceID voor de autorisatie regel voor de service bus wachtrij. ResourceId wordt weer gebruikt om de connection string voor de wachtrij op te halen.

**iotHubName**: dit veld is de naam van de IOT hub nadat randomValue is samengevoegd. 

**storageAccountName**: dit veld is de naam van het opslag account nadat randomValue is samengevoegd. 

**service_bus_namespace**: dit veld is de naam ruimte nadat randomValue is samengevoegd.

**service_bus_queue**: dit veld is de service bus wachtrij naam nadat randomValue is samengevoegd.

**sbVersion**: de versie van de Service Bus-API die moet worden gebruikt. In dit geval is het ' 2017-04-01 '.

### <a name="resources-storage-account-and-container"></a>Bronnen: opslag account en container

De eerste resource die wordt gemaakt, is het opslag account, samen met de container waarnaar berichten worden doorgestuurd. De container is een resource onder het opslag account. Het bevat een `dependsOn` -component voor het opslag account, waarvoor het opslag account moet worden gemaakt vóór de container.

Dit gedeelte ziet er als volgt uit:

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

### <a name="resources-service-bus-namespace-and-queue"></a>Resources: naam ruimte en wachtrij Service Bus

De tweede resource die wordt gemaakt, is de Service Bus naam ruimte, samen met de Service Bus wachtrij waarnaar berichten worden doorgestuurd. De SKU is ingesteld op Standard. De API-versie wordt opgehaald uit de variabelen. Het wordt ook ingesteld om de Service Bus naam ruimte te activeren bij het implementeren van deze sectie (status: actief). 

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

In deze sectie wordt de Service Bus wachtrij gemaakt. Dit onderdeel van het script bevat een `dependsOn` -component waarmee wordt gegarandeerd dat de naam ruimte wordt gemaakt vóór de wachtrij.

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

### <a name="resources-iot-hub-and-message-routing"></a>Bronnen: IOT hub en bericht routering

Nu het opslag account en de Service Bus wachtrij zijn gemaakt, kunt u de IoT Hub maken waarmee berichten naar deze worden doorgestuurd. De RM-sjabloon `dependsOn` maakt gebruik van componenten, zodat deze niet de hub probeert te maken voordat de service bus resources en het opslag account zijn gemaakt. 

Hier volgt het eerste deel van de sectie IoT Hub. Dit deel van de sjabloon stelt de afhankelijkheden in en begint met de eigenschappen.

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

De volgende sectie is de sectie voor de routerings configuratie voor berichten voor de IOT-hub. Eerste is de sectie voor de eind punten. In dit deel van de sjabloon worden de routering eindpunten voor de Service Bus wachtrij en het opslag account, met inbegrip van de verbindings reeksen, ingesteld.

Als u de connection string voor de wachtrij wilt maken, hebt u de queueAuthorizationRulesResourcedId nodig, die inline wordt opgehaald. Als u de connection string voor het opslag account wilt maken, haalt u de primaire opslag sleutel op en gebruikt u deze in de indeling voor de connection string.

De eindpunt configuratie is ook de locatie waar u de BLOB- `AVRO` indeling `JSON`instelt op of.

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

Deze volgende sectie is voor het bericht route naar de eind punten. Er is één voor elk eind punt ingesteld, dus er is een voor de Service Bus wachtrij en één voor de container van het opslag account.

Houd er rekening mee dat de query voorwaarde voor de berichten die worden doorgestuurd `level="storage"`naar de opslag, en de query voorwaarde voor de berichten die worden doorgestuurd naar de `level="critical"`service bus wachtrij.

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

Deze JSON toont de rest van de sectie IoT Hub, die standaard informatie en de SKU voor de hub bevat.

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

### <a name="resources-service-bus-queue-authorization-rules"></a>Resources: autorisatie regels voor Service Bus-wachtrij

De autorisatie regel voor de Service Bus-wachtrij wordt gebruikt om de connection string voor de Service Bus wachtrij op te halen. Er wordt een `dependsOn` -component gebruikt om ervoor te zorgen dat deze niet wordt gemaakt vóór de service bus naam ruimte en de service bus wachtrij.

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

### <a name="resources-consumer-group"></a>Resources: consumenten groep

In deze sectie maakt u een Consumer groep voor de IoT Hub gegevens die door de Azure Stream Analytics in het tweede deel van deze zelf studie worden gebruikt.

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

### <a name="resources-outputs"></a>Resources: uitvoer

Als u een waarde wilt verzenden naar het implementatie script dat moet worden weer gegeven, gebruikt u een sectie output. Dit deel van de sjabloon retourneert de connection string voor de Service Bus wachtrij. Het retour neren van een waarde is niet vereist. deze is opgenomen als een voor beeld van het retour neren van resultaten naar het aanroepende script.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>De RM-sjabloon implementeren

Als u de sjabloon wilt implementeren in azure, uploadt u de sjabloon en het parameter bestand naar Azure Cloud Shell en voert u vervolgens een script uit om de sjabloon te implementeren. Open Azure Cloud Shell en meld u aan. In dit voor beeld wordt Power shell gebruikt.

Als u de bestanden wilt uploaden, selecteert u het pictogram **bestanden uploaden/downloaden** in de menu balk en kiest u vervolgens uploaden.

![Cloud Shell menu balk met gemarkeerde upload-en download bestanden](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Gebruik de Verkenner die u wilt gebruiken om de bestanden op uw lokale schijf te zoeken en te selecteren en klik vervolgens op **openen**.

Nadat de bestanden zijn geüpload, wordt een dialoog venster met resultaten weer gegeven zoals in de volgende afbeelding.

![Cloud Shell menu balk met gemarkeerde upload-en download bestanden](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

De bestanden worden geüpload naar de share die wordt gebruikt door uw Cloud Shell-exemplaar. 

Voer het script uit om de implementatie uit te voeren. Met de laatste regel van dit script wordt de variabele opgehaald die is ingesteld om te worden geretourneerd: de Service Bus wachtrij connection string.

In het script worden de volgende variabelen ingesteld en gebruikt:

**$RGName** is de naam van de resource groep waarnaar de sjabloon moet worden geïmplementeerd. Dit veld wordt gemaakt voordat de sjabloon wordt geïmplementeerd.

**$Location** is de Azure-locatie die moet worden gebruikt voor de sjabloon, zoals ' westelijke '.

**implementatiemap** is een naam die u toewijst aan de implementatie om de geretourneerde variabele waarde op te halen.

Hier is het Power shell-script. Kopieer dit Power shell-script en plak het in het venster Cloud Shell en druk vervolgens op ENTER om het uit te voeren.

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

Als u script fouten hebt, kunt u het script lokaal bewerken, het opnieuw uploaden naar de Cloud Shell en het script opnieuw uitvoeren. Wanneer het uitvoeren van het script is voltooid, gaat u verder met de volgende stap.

## <a name="create-simulated-device"></a>Een gesimuleerd apparaat maken

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Bericht routering weer geven in de portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u alle resources hebt ingesteld en de bericht routes zijn geconfigureerd, gaat u verder met de volgende zelf studie om te leren hoe u de informatie over de gerouteerde berichten kunt verwerken en weer geven.

> [!div class="nextstepaction"]
> [Deel 2: de routerings resultaten van het bericht weer geven](tutorial-routing-view-message-routing-results.md)

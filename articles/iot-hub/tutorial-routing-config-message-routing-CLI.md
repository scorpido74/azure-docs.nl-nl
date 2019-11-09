---
title: Bericht routering voor Azure IoT Hub configureren met behulp van Azure CLI | Microsoft Docs
description: Configureer bericht routering voor Azure IoT Hub met behulp van de Azure CLI. Route naar een opslag account of een Service Bus wachtrij, afhankelijk van de eigenschappen in het bericht.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 844ed44a588bfd789e7e2dcf2b9fecda3d51329b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890371"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Zelf studie: de Azure CLI gebruiken om IoT Hub bericht routering te configureren

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Het script downloaden (optioneel)

Voor het tweede deel van deze zelf studie downloadt en voert u een Visual Studio-toepassing uit om berichten naar de IoT Hub te verzenden. Er is een map in de down load die de Azure Resource Manager sjabloon en het parameter bestand bevat, evenals de Azure CLI-en Power shell-scripts.

Als u het voltooide script wilt weer geven, downloadt u de [Azure C# IOT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)-voor beelden. Pak het zip-bestand master. Het Azure CLI-script bevindt zich in/iot-hub/Tutorials/Routing/SimulatedDevice/resources/als **iothub_routing_cli. azcli**.

## <a name="use-the-azure-cli-to-create-your-resources"></a>De Azure CLI gebruiken om uw resources te maken

Kopieer en plak het onderstaande script in Cloud Shell en druk op ENTER. Het script wordt één regel per keer uitgevoerd. In dit eerste gedeelte van het script worden de basis resources voor deze zelf studie gemaakt, met inbegrip van het opslag account, het IoT Hub, de Service Bus naam ruimte en de Service Bus wachtrij. Als u de rest van de zelf studie doorloopt, kopieert u elk script blok en plakt u het in Cloud Shell om het uit te voeren.

> [!TIP]
> Een tip over fout opsporing: dit script maakt gebruik van het vervolg symbool (de back slash `\`) om het script beter leesbaar te maken. Als u een probleem hebt met het uitvoeren van het script, controleert u of uw Cloud Shell-sessie wordt uitgevoerd `bash` en of er geen spaties na een van de backslashes staan.
> 

Er zijn verschillende resource namen die wereld wijd uniek moeten zijn, zoals de naam van de IoT Hub en de naam van het opslag account. Om dit eenvoudiger te maken, worden deze resource namen toegevoegd met een wille keurige alfanumerieke waarde met de naam *randomValue*. De randomValue wordt eenmaal aan het begin van het script gegenereerd en aan de resource namen toegevoegd, zoals nodig is in het script. Als u niet wilt dat deze wille keurig worden ingesteld, kunt u deze instellen op een lege teken reeks of op een specifieke waarde. 

> [!IMPORTANT]
> De variabelen die in het eerste script zijn ingesteld, worden ook gebruikt door het routerings script, dus voer alle scripts uit in dezelfde Cloud Shell sessie. Als u een nieuwe sessie opent om het script voor het instellen van de route ring uit te voeren, ontbreken er waarden in verschillende variabelen.
>

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, 
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName
```

Nu de basis bronnen zijn ingesteld, kunt u de route ring van berichten configureren.

## <a name="set-up-message-routing"></a>Berichtroutering instellen

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Gebruik [AZ IOT hub Routing-endpoint Create](/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest#az-iot-hub-routing-endpoint-create)om een routerings eindpunt te maken. Gebruik [AZ IOT hub route Create](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create)om de bericht route voor het eind punt te maken.

### <a name="route-to-a-storage-account"></a>Een opslag account door sturen

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Stel eerst het eind punt in voor het opslag account en stel vervolgens de route in. 

Dit zijn de variabelen die worden gebruikt door het script dat moet worden ingesteld binnen uw Cloud Shell-sessie:

**storageConnectionString**: deze waarde wordt opgehaald uit het opslag account dat in het vorige script is ingesteld. Het wordt gebruikt door de bericht routering om toegang te krijgen tot het opslag account.

  **resourceGroup**: er zijn twee exemplaren van de resource groep. Stel ze in voor de resource groep.

**eind punt-subscriptionid**: dit veld wordt ingesteld op de Azure subscriptionID voor het eind punt. 

**endpointType**: dit veld is het type eind punt. Deze waarde moet worden ingesteld op `azurestoragecontainer`, `eventhub`, `servicebusqueue`of `servicebustopic`. Stel deze in op `azurestoragecontainer`voor uw doel einden.

**iotHubName**: dit veld is de naam van de hub die de route ring gaat uitvoeren.

**containerName**: dit veld bevat de naam van de container in het opslag account waarnaar de gegevens worden geschreven.

**Encoding**: dit veld wordt `avro` of `json`. Dit geeft de indeling van de opgeslagen gegevens aan.

**route**naam: dit veld bevat de naam van de route die u instelt. 

**eind punt**: dit veld is de naam die het eind punt aangeeft. 

**ingeschakeld**: dit veld wordt standaard ingesteld op `true`, wat aangeeft dat de bericht route moet worden ingeschakeld nadat deze is gemaakt.

**voor waarde**: dit veld is de query die wordt gebruikt om te filteren op berichten die naar dit eind punt worden verzonden. De query voorwaarde voor de berichten die naar de opslag worden doorgestuurd, is `level="storage"`.

Kopieer dit script en plak het in het Cloud Shell venster en voer dit uit.

```azurecli
##### ROUTING FOR STORAGE ##### 

endpointName="ContosoStorageEndpoint"
endpointType="azurestoragecontainer"
routeName="ContosoStorageRoute"
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)
```

De volgende stap is het maken van het eind punt van de route ring voor het opslag account. U geeft ook de container op waarin de resultaten worden opgeslagen. De container is eerder gemaakt toen het opslag account werd gemaakt.

```azurecli
# Create the routing endpoint for storage.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName \
  --resource-group $resourceGroup \
  --encoding avro
```

Maak vervolgens de route voor het eind punt van de opslag. De bericht route geeft aan waar de berichten moeten worden verzonden die voldoen aan de query specificatie. 

```azurecli
# Create the route for the storage endpoint.
az iot hub route create \
  --name $routeName \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
```

### <a name="route-to-a-service-bus-queue"></a>Een Service Bus wachtrij omleiden

Stel nu de routering in voor de Service Bus-wachtrij. Als u de connection string voor de Service Bus wachtrij wilt ophalen, moet u een autorisatie regel maken waarvoor de juiste rechten zijn gedefinieerd. Met het volgende script maakt u een autorisatie regel voor de Service Bus wachtrij met de naam `sbauthrule`, en stelt u de rechten in voor `Listen Manage Send`. Zodra deze autorisatie regel is gedefinieerd, kunt u deze gebruiken om de connection string voor de wachtrij op te halen.

```azurecli
# Create the authorization rule for the Service Bus queue.
az servicebus queue authorization-rule create \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --rights Listen Manage Send \
  --subscription $subscriptionID
```

Gebruik nu de autorisatie regel om de connection string op te halen voor de Service Bus wachtrij.

```azurecli
# Get the Service Bus queue connection string.
# The "-o tsv" ensures it is returned without the default double-quotes.
sbqConnectionString=$(az servicebus queue authorization-rule keys list \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --subscription $subscriptionID \
  --query primaryConnectionString -o tsv)

# Show the Service Bus queue connection string.
echo "service bus queue connection string = " $sbqConnectionString
```

Stel nu het eind punt van de route ring in en de bericht route voor de Service Bus wachtrij. Dit zijn de variabelen die worden gebruikt door het script dat moet worden ingesteld binnen uw Cloud Shell-sessie:

**eind punt**: dit veld is de naam die het eind punt aangeeft. 

**endpointType**: dit veld is het type eind punt. Deze waarde moet worden ingesteld op `azurestoragecontainer`, `eventhub`, `servicebusqueue`of `servicebustopic`. Stel deze in op `servicebusqueue`voor uw doel einden.

**route**naam: dit veld bevat de naam van de route die u instelt. 

**voor waarde**: dit veld is de query die wordt gebruikt om te filteren op berichten die naar dit eind punt worden verzonden. De query voorwaarde voor de berichten die naar de Service Bus wachtrij worden doorgestuurd, is `level="critical"`.

Dit is de Azure CLI voor het eind punt van de route ring en de bericht route voor de Service Bus wachtrij.

```azurecli
endpointName="ContosoSBQueueEndpoint"
endpointType="ServiceBusQueue"
routeName="ContosoSBQueueRoute"
condition='level="critical"'

# Set up the routing endpoint for the Service Bus queue.
# This uses the Service Bus queue connection string.
az iot hub routing-endpoint create \
  --connection-string $sbqConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --resource-group $resourceGroup 

# Set up the message route for the Service Bus queue endpoint.
az iot hub route create --name $routeName \
  --hub-name $iotHubName \
  --source-type devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
  ```

### <a name="view-message-routing-in-the-portal"></a>Bericht routering weer geven in de portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u de resources hebt ingesteld en de bericht routes hebt geconfigureerd, gaat u naar de volgende zelf studie om te leren hoe u berichten verzendt naar de IoT-hub en hoe deze worden doorgestuurd naar de verschillende bestemmingen. 

> [!div class="nextstepaction"]
> [Deel 2: de routerings resultaten van het bericht weer geven](tutorial-routing-view-message-routing-results.md)

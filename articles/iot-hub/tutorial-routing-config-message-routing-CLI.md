---
title: Berichtroutering configureren voor Azure IoT Hub met de Azure CLI
description: Berichtroutering configureren voor Azure IoT Hub met de Azure CLI. Afhankelijk van de eigenschappen in het bericht, route naar een opslagaccount of een Service Bus wachtrij.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 056dac7977115f97892d8dbfde0710e00237804e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78674347"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Zelfstudie: De Azure CLI gebruiken om de routeringsroute van iot-hubberichten te configureren

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Het script downloaden (optioneel)

Voor het tweede deel van deze zelfstudie download t u een Visual Studio-toepassing om berichten naar de IoT-hub te verzenden. Er is een map in de download die de Azure Resource Manager-sjabloon en parametersbestand bevat, evenals de Azure CLI- en PowerShell-scripts.

Als u het voltooide script wilt bekijken, downloadt u de [Azure IoT C#-voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Rits het bestand master.zip uit. Het Azure CLI-script bevindt zich in /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ als **iothub_routing_cli.azcli**.

## <a name="use-the-azure-cli-to-create-your-resources"></a>De Azure CLI gebruiken om uw resources te maken

Kopieer en plak het onderstaande script in Cloud Shell en druk op Enter. Het loopt het script een regel tegelijk. In dit eerste gedeelte van het script worden de basisbronnen voor deze zelfstudie gemaakt, waaronder het opslagaccount, iot-hub, servicebusnaamruimte en de wachtrij servicebus. Terwijl u door de rest van de zelfstudie gaat, kopieert u elk blok script en plakt u het in Cloud Shell om het uit te voeren.

> [!TIP]
> Een tip over foutopsporing: dit script gebruikt het `\`vervolgsymbool (de backslash) om het script leesbaarder te maken. Als u een probleem hebt met het uitvoeren van `bash` het script, controleert u of uw Cloud Shell-sessie wordt uitgevoerd en dat er geen spaties zijn na een van de backslashes.
> 

Er zijn verschillende resourcenamen die wereldwijd uniek moeten zijn, zoals de naam IoT Hub en de naam van het opslagaccount. Om dit gemakkelijker te maken, worden deze resourcenamen toegevoegd met een willekeurige alfanumerieke waarde genaamd *randomValue*. De randomValue wordt eenmaal gegenereerd aan de bovenkant van het script en toegevoegd aan de resource namen indien nodig in het script. Als u niet wilt dat het willekeurig is, u het instellen op een lege tekenreeks of op een specifieke waarde. 

> [!IMPORTANT]
> De variabelen die in het oorspronkelijke script zijn ingesteld, worden ook gebruikt door het routeringsscript, dus voer alle scripts uit in dezelfde Cloud Shell-sessie. Als u een nieuwe sessie opent om het script uit te voeren voor het instellen van de routering, ontbreken verschillende variabelen waarden.
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
az extension add --name azure-iot

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

Nu de basisresources zijn ingesteld, u de berichtroutering configureren.

## <a name="set-up-message-routing"></a>Berichtroutering instellen

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Als u een routeringseindpunt wilt maken, gebruikt u [het leiden van az-hubrouting-endpoint.](/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest#az-iot-hub-routing-endpoint-create) Als u de berichtroute voor het eindpunt wilt maken, gebruikt u [az iot-hubroute maken](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create).

### <a name="route-to-a-storage-account"></a>Route naar een opslagaccount

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Stel eerst het eindpunt in voor het opslagaccount en stel vervolgens de route in. 

Dit zijn de variabelen die worden gebruikt door het script die moeten worden ingesteld binnen uw Cloud Shell-sessie:

**storageConnectionString**: Deze waarde wordt opgehaald uit het opslagaccount dat in het vorige script is ingesteld. Het wordt gebruikt door de berichtroutering om toegang te krijgen tot het opslagaccount.

  **resourceGroep**: Er zijn twee exemplaren van resourcegroep - stel deze in op uw resourcegroep.

**endpointsubscriptionID**: dit veld is ingesteld op de Azure-abonnements-ID voor het eindpunt. 

**endpointType:** dit veld is het type eindpunt. Deze waarde moet `azurestoragecontainer`worden `eventhub` `servicebusqueue`ingesteld `servicebustopic`op , , of . Voor uw doeleinden hier, `azurestoragecontainer`stel het in op.

**iotHubName**: Dit veld is de naam van de hub die de routering zal doen.

**containerNaam**: Dit veld is de naam van de container in het opslagaccount waarop gegevens worden geschreven.

**codering**: Dit veld `avro` is `json`een van beide of . Dit geeft de indeling van de opgeslagen gegevens aan.

**routeNaam:** Dit veld is de naam van de route die u instelt. 

**endpointName:** Dit veld is de naam die het eindpunt identificeert. 

**ingeschakeld**: Dit veld `true`geeft standaard aan , zodat de berichtroute moet worden ingeschakeld nadat deze is gemaakt.

**voorwaarde:** dit veld is de query die wordt gebruikt om te filteren op de berichten die naar dit eindpunt worden verzonden. De queryvoorwaarde voor de berichten die `level="storage"`naar de opslag worden doorgestuurd, is .

Kopieer dit script en plak het in uw Cloud Shell-venster en voer het uit.

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

De volgende stap is het maken van het routeringseindpunt voor het opslagaccount. U geeft ook de container op waarin de resultaten worden opgeslagen. De container is eerder gemaakt toen het opslagaccount werd gemaakt.

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

Maak vervolgens de route voor het opslageindpunt. De berichtroute geeft aan waar u de berichten moet verzenden die voldoen aan de queryspecificatie. 

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

### <a name="route-to-a-service-bus-queue"></a>Route naar een wachtrij voor servicebussen

Stel nu de routering in voor de Service Bus-wachtrij. Als u de verbindingstekenreeks voor de wachtrij servicebus wilt ophalen, moet u een autorisatieregel maken waarop de juiste rechten zijn gedefinieerd. In het volgende script wordt een autorisatieregel gemaakt voor de wachtrij Servicebus die wordt aangeroepen `sbauthrule`en worden de rechten ingesteld op `Listen Manage Send`. Zodra deze autorisatieregel is gedefinieerd, u deze gebruiken om de verbindingstekenreeks voor de wachtrij op te halen.

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

Gebruik nu de autorisatieregel om de verbindingstekenreeks op te halen in de wachtrij servicebus.

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

Stel nu het routeeindpunt en de berichtroute in voor de wachtrij servicebus. Dit zijn de variabelen die worden gebruikt door het script die moeten worden ingesteld binnen uw Cloud Shell-sessie:

**endpointName:** Dit veld is de naam die het eindpunt identificeert. 

**endpointType:** dit veld is het type eindpunt. Deze waarde moet `azurestoragecontainer`worden `eventhub` `servicebusqueue`ingesteld `servicebustopic`op , , of . Voor uw doeleinden hier, `servicebusqueue`stel het in op.

**routeNaam:** Dit veld is de naam van de route die u instelt. 

**voorwaarde:** dit veld is de query die wordt gebruikt om te filteren op de berichten die naar dit eindpunt worden verzonden. De queryvoorwaarde voor de berichten die worden doorgestuurd `level="critical"`naar de wachtrij servicebus is .

Hier vindt u het Azure CLI voor het routeringseindpunt en de berichtroute voor de wachtrij servicebus.

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

### <a name="view-message-routing-in-the-portal"></a>Berichtroutering weergeven in de portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u de resources hebt ingesteld en de berichtroutes hebt geconfigureerd, gaat u door naar de volgende zelfstudie om te leren hoe u berichten naar de IoT-hub verzenden en ze naar de verschillende bestemmingen laten worden doorgestuurd. 

> [!div class="nextstepaction"]
> [Deel 2 - Bekijk de resultaten van de routeringsresultaten van het bericht](tutorial-routing-view-message-routing-results.md)

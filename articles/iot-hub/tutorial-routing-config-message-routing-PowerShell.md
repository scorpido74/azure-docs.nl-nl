---
title: Configureer bericht routering voor Azure IoT Hub met Azure PowerShell
description: Configureer bericht routering voor Azure IoT Hub met behulp van Azure PowerShell. Route naar een opslag account of een Service Bus wachtrij, afhankelijk van de eigenschappen in het bericht.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 68338c56419316e561bb072c1a0555e89d3de85b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74084444"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Zelf studie: Azure PowerShell gebruiken om IoT Hub bericht routering te configureren

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Het script downloaden (optioneel)

Voor het tweede deel van deze zelf studie downloadt en voert u een Visual Studio-toepassing uit om berichten naar de IoT Hub te verzenden. Er is een map in de down load die de Azure Resource Manager sjabloon en het parameter bestand bevat, evenals de Azure CLI-en Power shell-scripts. 

Als u het voltooide script wilt weer geven, downloadt u de [Azure IOT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)-voor beelden. Pak het zip-bestand master. Het Azure CLI-script bevindt zich in/iot-hub/Tutorials/Routing/SimulatedDevice/resources/als **iothub_routing_psh. ps1**.

## <a name="create-your-resources"></a>Uw resources maken

Begin met het maken van de resources met Power shell.

### <a name="use-powershell-to-create-your-base-resources"></a>Power shell gebruiken voor het maken van uw basis bronnen

Kopieer en plak het onderstaande script in Cloud Shell en druk op ENTER. Het script wordt één regel per keer uitgevoerd. In dit eerste gedeelte van het script worden de basis resources voor deze zelf studie gemaakt, met inbegrip van het opslag account, het IoT Hub, de Service Bus naam ruimte en de Service Bus wachtrij. Als u de zelf studie doorloopt, kopieert u elk script blok en plakt u het in Cloud Shell om het uit te voeren.

Er zijn verschillende resource namen die wereld wijd uniek moeten zijn, zoals de naam van de IoT Hub en de naam van het opslag account. Om dit eenvoudiger te maken, worden deze resource namen toegevoegd met een wille keurige alfanumerieke waarde met de naam *randomValue*. De randomValue wordt eenmaal aan het begin van het script gegenereerd en aan de resource namen toegevoegd, zoals nodig is in het script. Als u niet wilt dat deze wille keurig worden ingesteld, kunt u deze instellen op een lege teken reeks of op een specifieke waarde. 

> [!IMPORTANT]
> De variabelen die in het eerste script zijn ingesteld, worden ook gebruikt door het routerings script, dus voer alle scripts uit in dezelfde Cloud Shell sessie. Als u een nieuwe sessie opent om het script voor het instellen van de route ring uit te voeren, ontbreken er waarden in verschillende variabelen. 
>

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>Een gesimuleerd apparaat maken

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

Nu de basis bronnen zijn ingesteld, kunt u de route ring van berichten configureren.

## <a name="set-up-message-routing"></a>Berichtroutering instellen

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Als u een eind punt voor een route ring wilt maken, gebruikt u [add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint). Als u de berichten route voor het eind punt wilt maken, gebruikt u [add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute).

### <a name="route-to-a-storage-account"></a>Een opslag account door sturen 

Stel eerst het eind punt in voor het opslag account en maak vervolgens de bericht route.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Dit zijn de variabelen die worden gebruikt door het script dat moet worden ingesteld binnen uw Cloud Shell-sessie:

**resourceGroup**: er zijn twee exemplaren van dit veld. Stel beide items in voor uw resource groep.

**naam**: dit veld bevat de naam van het IOT hub waarop het bewerkings plan van toepassing moet zijn.

**eind punt**: dit veld is de naam die het eind punt aangeeft. 

**endpointType**: dit veld is het type eind punt. Deze waarde moet worden ingesteld op `azurestoragecontainer`, `eventhub`, `servicebusqueue`of `servicebustopic`. Voor uw doel einden stelt u deze `azurestoragecontainer`in op.

**subscriptionid**: dit veld is ingesteld op de subscriptionid voor uw Azure-account.

**storageConnectionString**: deze waarde wordt opgehaald uit het opslag account dat in het vorige script is ingesteld. Het wordt gebruikt door de route ring om toegang te krijgen tot het opslag account.

**containerName**: dit veld bevat de naam van de container in het opslag account waarnaar de gegevens worden geschreven.

**Encoding**: Stel dit veld in op `AVRO` ofwel `JSON`of. Hiermee wordt de indeling van de opgeslagen gegevens aangegeven. De standaard waarde is AVRO.

**route**naam: dit veld bevat de naam van de route die u instelt. 

**voor waarde**: dit veld is de query die wordt gebruikt om te filteren op berichten die naar dit eind punt worden verzonden. De query voorwaarde voor de berichten die worden doorgestuurd naar Storage is `level="storage"`.

**ingeschakeld**: dit veld wordt standaard `true`ingesteld op om aan te geven dat de bericht route moet worden ingeschakeld nadat deze is gemaakt.

Kopieer dit script en plak het in het Cloud Shell venster.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

De volgende stap is het maken van het eind punt van de route ring voor het opslag account. U geeft ook de container op waarin de resultaten worden opgeslagen. De container is gemaakt tijdens het maken van het opslag account.

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

Maak vervolgens de bericht route voor het eind punt van de opslag. De bericht route geeft aan waar de berichten moeten worden verzonden die voldoen aan de query specificatie.

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>Een Service Bus wachtrij omleiden

Stel nu de routering in voor de Service Bus-wachtrij. Als u de connection string voor de Service Bus wachtrij wilt ophalen, moet u een autorisatie regel maken waarvoor de juiste rechten zijn gedefinieerd. Met het volgende script maakt u een autorisatie regel voor de naam `sbauthrule`van de service bus wachtrij en stelt `Listen Manage Send`u de rechten in op. Zodra deze autorisatie regel is ingesteld, kunt u deze gebruiken om de connection string voor de wachtrij op te halen.

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

Gebruik nu de autorisatie regel om de Service Bus-wachtrij sleutel op te halen. Deze autorisatie regel wordt gebruikt om de connection string verderop in het script op te halen.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Stel nu het eind punt van de route ring in en de bericht route voor de Service Bus wachtrij. Dit zijn de variabelen die worden gebruikt door het script dat moet worden ingesteld binnen uw Cloud Shell-sessie:

**eind punt**: dit veld is de naam die het eind punt aangeeft. 

**endpointType**: dit veld is het type eind punt. Deze waarde moet worden ingesteld op `azurestoragecontainer`, `eventhub`, `servicebusqueue`of `servicebustopic`. Voor uw doel einden stelt u deze `servicebusqueue`in op.

**route**naam: dit veld bevat de naam van de route die u instelt. 

**voor waarde**: dit veld is de query die wordt gebruikt om te filteren op berichten die naar dit eind punt worden verzonden. De query voorwaarde voor de berichten die worden doorgestuurd naar de wachtrij voor Service Bus `level="critical"`is.

Hier volgt de Azure PowerShell voor de route ring van berichten voor de Service Bus wachtrij.

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# Add the routing endpoint, using the connection string property from the key.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>Bericht routering weer geven in de portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u de resources hebt ingesteld en de bericht routes hebt geconfigureerd, gaat u naar de volgende zelf studie om te leren hoe u berichten verzendt naar de IoT-hub en hoe deze worden doorgestuurd naar de verschillende bestemmingen. 

> [!div class="nextstepaction"]
> [Deel 2: de routerings resultaten van het bericht weer geven](tutorial-routing-view-message-routing-results.md)
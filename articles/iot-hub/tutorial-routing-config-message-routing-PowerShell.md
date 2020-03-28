---
title: Berichtroutering configureren voor Azure IoT Hub met Azure PowerShell
description: Beheer van berichtroutering voor Azure IoT Hub met Azure PowerShell. Afhankelijk van de eigenschappen in het bericht, route naar een opslagaccount of een Service Bus wachtrij.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 68338c56419316e561bb072c1a0555e89d3de85b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084444"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Zelfstudie: Azure PowerShell gebruiken om de routeringsroute van iot-hubberichten te configureren

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Het script downloaden (optioneel)

Voor het tweede deel van deze zelfstudie download t u een Visual Studio-toepassing om berichten naar de IoT-hub te verzenden. Er is een map in de download die de Azure Resource Manager-sjabloon en parametersbestand bevat, evenals de Azure CLI- en PowerShell-scripts. 

Als u het voltooide script wilt bekijken, downloadt u de [Azure IoT C#-voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Rits het bestand master.zip uit. Het Azure CLI-script bevindt zich in /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ als **iothub_routing_psh.ps1**.

## <a name="create-your-resources"></a>Uw resources maken

Begin met het maken van de resources met PowerShell.

### <a name="use-powershell-to-create-your-base-resources"></a>PowerShell gebruiken om uw basisbronnen te maken

Kopieer en plak het onderstaande script in Cloud Shell en druk op Enter. Het loopt het script een regel tegelijk. In dit eerste gedeelte van het script worden de basisbronnen voor deze zelfstudie gemaakt, waaronder het opslagaccount, iot-hub, servicebusnaamruimte en de wachtrij servicebus. Terwijl u door de zelfstudie gaat, kopieert u elk blok script en plakt u het in Cloud Shell om het uit te voeren.

Er zijn verschillende resourcenamen die wereldwijd uniek moeten zijn, zoals de naam IoT Hub en de naam van het opslagaccount. Om dit gemakkelijker te maken, worden deze resourcenamen toegevoegd met een willekeurige alfanumerieke waarde genaamd *randomValue*. De randomValue wordt eenmaal gegenereerd aan de bovenkant van het script en toegevoegd aan de resource namen indien nodig in het script. Als u niet wilt dat het willekeurig is, u het instellen op een lege tekenreeks of op een specifieke waarde. 

> [!IMPORTANT]
> De variabelen die in het oorspronkelijke script zijn ingesteld, worden ook gebruikt door het routeringsscript, dus voer alle scripts uit in dezelfde Cloud Shell-sessie. Als u een nieuwe sessie opent om het script uit te voeren voor het instellen van de routering, ontbreken verschillende variabelen waarden. 
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

Nu de basisresources zijn ingesteld, u de berichtroutering configureren.

## <a name="set-up-message-routing"></a>Berichtroutering instellen

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Als u een routeringseindpunt wilt maken, gebruikt u [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint). Als u de berichtenroute voor het eindpunt wilt maken, gebruikt u [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute).

### <a name="route-to-a-storage-account"></a>Route naar een opslagaccount 

Stel eerst het eindpunt in voor het opslagaccount en maak vervolgens de berichtroute.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Dit zijn de variabelen die worden gebruikt door het script die moeten worden ingesteld binnen uw Cloud Shell-sessie:

**resourceGroep**: Er zijn twee exemplaren van dit veld : deze instellen op uw resourcegroep.

**naam:** Dit veld is de naam van de IoT-hub waarop de routering van toepassing is.

**endpointName:** Dit veld is de naam die het eindpunt identificeert. 

**endpointType:** dit veld is het type eindpunt. Deze waarde moet `azurestoragecontainer`worden `eventhub` `servicebusqueue`ingesteld `servicebustopic`op , , of . Voor uw doeleinden hier, `azurestoragecontainer`stel het in op.

**abonnement-ID**: Dit veld is ingesteld op de abonnement-id voor uw Azure-account.

**storageConnectionString**: Deze waarde wordt opgehaald uit het opslagaccount dat in het vorige script is ingesteld. Het wordt gebruikt door de routering om toegang te krijgen tot het opslagaccount.

**containerNaam**: Dit veld is de naam van de container in het opslagaccount waarop gegevens worden geschreven.

**Codering**: Stel dit `AVRO` veld `JSON`in op een van beide of . Dit wijst het formaat van de opgeslagen gegevens aan. De standaard is AVRO.

**routeNaam:** Dit veld is de naam van de route die u instelt. 

**voorwaarde:** dit veld is de query die wordt gebruikt om te filteren op de berichten die naar dit eindpunt worden verzonden. De queryvoorwaarde voor de berichten die `level="storage"`naar de opslag worden doorgestuurd, is .

**ingeschakeld**: Dit veld `true`geeft standaard aan , zodat de berichtroute moet worden ingeschakeld nadat deze is gemaakt.

Kopieer dit script en plak het in het Venster Cloud Shell.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

De volgende stap is het maken van het routeringseindpunt voor het opslagaccount. U geeft ook de container op waarin de resultaten worden opgeslagen. De container is gemaakt toen het opslagaccount werd gemaakt.

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

Maak vervolgens de berichtroute voor het opslageindpunt. De berichtroute geeft aan waar u de berichten moet verzenden die voldoen aan de queryspecificatie.

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

### <a name="route-to-a-service-bus-queue"></a>Route naar een wachtrij voor servicebussen

Stel nu de routering in voor de Service Bus-wachtrij. Als u de verbindingstekenreeks voor de wachtrij servicebus wilt ophalen, moet u een autorisatieregel maken waarop de juiste rechten zijn gedefinieerd. In het volgende script wordt een autorisatieregel gemaakt voor de wachtrij Servicebus die wordt aangeroepen `sbauthrule`en worden de rechten ingesteld op `Listen Manage Send`. Zodra deze autorisatieregel is ingesteld, u deze gebruiken om de verbindingstekenreeks voor de wachtrij op te halen.

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

Gebruik nu de autorisatieregel om de wachtrijsleutel servicebus op te halen. Deze autorisatieregel wordt gebruikt om de verbindingstekenreeks later in het script op te halen.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Stel nu het routeeindpunt en de berichtroute in voor de wachtrij servicebus. Dit zijn de variabelen die worden gebruikt door het script die moeten worden ingesteld binnen uw Cloud Shell-sessie:

**endpointName:** Dit veld is de naam die het eindpunt identificeert. 

**endpointType:** dit veld is het type eindpunt. Deze waarde moet `azurestoragecontainer`worden `eventhub` `servicebusqueue`ingesteld `servicebustopic`op , , of . Voor uw doeleinden hier, `servicebusqueue`stel het in op.

**routeNaam:** Dit veld is de naam van de route die u instelt. 

**voorwaarde:** dit veld is de query die wordt gebruikt om te filteren op de berichten die naar dit eindpunt worden verzonden. De queryvoorwaarde voor de berichten die worden doorgestuurd `level="critical"`naar de wachtrij servicebus is .

Hier vindt u de Azure PowerShell voor de berichtroutering voor de wachtrij servicebus.

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

### <a name="view-message-routing-in-the-portal"></a>Berichtroutering weergeven in de portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u de resources hebt ingesteld en de berichtroutes hebt geconfigureerd, gaat u door naar de volgende zelfstudie om te leren hoe u berichten naar de IoT-hub verzenden en ze naar de verschillende bestemmingen laten worden doorgestuurd. 

> [!div class="nextstepaction"]
> [Deel 2 - Bekijk de resultaten van de routeringsresultaten van het bericht](tutorial-routing-view-message-routing-results.md)
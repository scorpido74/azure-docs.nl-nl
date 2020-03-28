---
title: Berichtroutering configureren voor Azure IoT Hub met Azure CLI
description: Berichtroutering configureren voor Azure IoT Hub met de Azure CLI en de Azure-portal
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 38a40d628b883c0e7ada824d47d3fdf3d29caf93
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084374"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Zelfstudie: De Azure CLI- en Azure-portal gebruiken om de routeringsroute van iot-hubberichten te configureren

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>De Azure CLI gebruiken om de basisresources te maken

Deze zelfstudie gebruikt de Azure CLI om de basisresources te maken en gebruikt vervolgens de [Azure-portal](https://portal.azure.com) om te laten zien hoe u berichtroutering configureert en het virtuele apparaat instelt voor tests.

Kopieer en plak het onderstaande script in Cloud Shell en druk op Enter. Het loopt het script een regel tegelijk. Hiermee worden de basisbronnen voor deze zelfstudie gemaakt, waaronder het opslagaccount, iot-hub, servicebusnaamruimte en de wachtrij voor servicebus.

Er zijn verschillende resourcenamen die wereldwijd uniek moeten zijn, zoals de naam IoT Hub en de naam van het opslagaccount. Om dit gemakkelijker te maken, worden deze resourcenamen toegevoegd met een willekeurige alfanumerieke waarde genaamd *randomValue*. De randomValue wordt eenmaal gegenereerd aan de bovenkant van het script en toegevoegd aan de resource namen indien nodig in het script. Als u niet wilt dat het willekeurig is, u het instellen op een lege tekenreeks of op een specifieke waarde.

> [!TIP]
> Een tip over foutopsporing: dit script gebruikt het `\`vervolgsymbool (de backslash) om het script leesbaarder te maken. Als u een probleem hebt met het uitvoeren van `bash` het script, controleert u of uw Cloud Shell-sessie wordt uitgevoerd en dat er geen spaties zijn na een van de backslashes.
>

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing queries.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults

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

```

Nu de basisresources zijn ingesteld, u de berichtroutering configureren in de [Azure-portal.](https://portal.azure.com)

## <a name="set-up-message-routing"></a>Berichtroutering instellen

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Route naar een opslagaccount

Stel nu de routering in voor het opslagaccount. Ga naar het deelvenster Berichtroutering en voeg een route toe. Definieer bij het toevoegen van de route een nieuw eindpunt voor de route. Nadat deze routering is ingesteld, worden berichten waarbij de **eigenschap niveau** is ingesteld op **opslag** automatisch naar een opslagaccount geschreven. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. Selecteer in de [Azure-portal](https://portal.azure.com) **Resourcegroepen**en selecteer vervolgens uw resourcegroep. In deze zelfstudie wordt gebruikgemaakt van **ContosoResources**.

2. Selecteer de IoT-hub onder de lijst met bronnen. In deze zelfstudie wordt gebruikgemaakt van **ContosoTestHub**.

3. Selecteer **Berichtroutering**. Selecteer in het deelvenster **Berichtroutering** de optie +**Toevoegen**. Selecteer **in** het deelvenster Een route toevoegen de optie +**Toevoegen** naast het veld Eindpunt om de ondersteunde eindpunten weer te geven, zoals weergegeven in de volgende afbeelding:

   ![Een eindpunt voor een route toevoegen](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. Selecteer **Blob-opslag**. U ziet het deelvenster **Een opslageindpunt toevoegen.**

   ![Een eindpunt toevoegen](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. Voer een naam in voor het eindpunt. Deze zelfstudie maakt gebruik van **ContosoStorageEndpoint**.

6. Selecteer **Een container kiezen**. Er wordt een lijst met opslagaccounts weergegeven. Selecteer het account dat u hebt ingesteld in de voorbereidende stappen. In deze zelfstudie wordt gebruikgemaakt van **contosostorage**. Er wordt een lijst met containers in die opslagaccount weergegeven. **Selecteer** de container die u in de voorbereidingsstappen hebt ingesteld. In deze zelfstudie wordt gebruikgemaakt van **contosoresults**. U keert terug naar het **deelvenster Een opslageindpunt toevoegen** en ziet de selecties die u hebt gemaakt.

7. Stel de codering in op AVRO of JSON. Gebruik ten behoeve van deze zelfstudie de standaardinstellingen voor de rest van de velden. Dit veld wordt grijs weergegeven als de geselecteerde regio geen JSON-codering ondersteunt.,

   > [!NOTE]
   > U kunt de indeling van de blob-naam instellen met de **indeling van blobbestandsnaam**. De standaardwaarde is `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. De indeling moet {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH}, en {mm} bevatten, in willekeurige volgorde.
   >
   > Als u bijvoorbeeld de indeling van de standaardnaam voor het blobbestand gebruikt, de naam van de hub ContosoTestHub is en de datum en tijd 30 oktober 2018 om 10:56 uur, dan ziet de blobnaam er als volgt uit: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > De blobs worden geschreven in de Avro-indeling.
   >

8. Selecteer **Maken** om het opslageindpunt te maken en het aan de route toe te voegen. U gaat terug naar het deelvenster **Route toevoegen**.

9. Vul nu de rest van de informatie voor de routeringsquery in. Deze query specificeert de criteria voor het verzenden van berichten naar de opslagcontainer die u zojuist hebt toegevoegd als eindpunt. Vul de velden in op het scherm.

   **Naam**: voer een naam in voor uw routeringsquery. Deze zelfstudie maakt gebruik van **ContosoStorageRoute**.

   **Eindpunt**: geeft het eindpunt weer dat u net hebt ingesteld.

   **Gegevensbron**: selecteer **Telemetrieberichten apparaat** uit de vervolgkeuzelijst.

   **Route inschakelen:** zorg ervoor dat `enabled`dit veld is ingesteld op .
   
   **Routeringsquery**: voer `level="storage"` in als querytekenreeks.

   ![Een routeringsquery maken voor het opslagaccount](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Selecteer **Opslaan**. Wanneer dit is voltooid, gaat u terug naar het deelvenster Berichtroutering, waar u uw nieuwe routeringsquery voor opslag kunt zien. Sluit het deelvenster Routes, waarna u terugkeert naar de pagina Resourcegroep.

### <a name="route-to-a-service-bus-queue"></a>Route naar een wachtrij voor servicebussen

Stel nu de routering in voor de Service Bus-wachtrij. Ga naar het deelvenster Berichtroutering en voeg een route toe. Definieer bij het toevoegen van de route een nieuw eindpunt voor de route. Nadat deze route is ingesteld, worden berichten waarin de **eigenschap niveau** is ingesteld op **kritiek,** naar de wachtrij servicebus geschreven, die een Logische App activeert, die vervolgens een e-mail met de informatie verzendt.

1. Selecteer op de pagina Resourcegroep uw IoT-hub en selecteer **Vervolgens Berichtroutering**.

2. Selecteer in het deelvenster **Berichtroutering** de optie +**Toevoegen**.

3. Selecteer +**Toevoegen** naast het veld Eindpunt in het deelvenster **Een route toevoegen.** Selecteer een **Service Bus-wachtrij**. Het deelvenster **Service Bus-eindpunt toevoegen** wordt weergegeven.

   ![Een eindpunt van een servicebus toevoegen](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Vul de velden in:

   **Eindpuntnaam**: voer een naam in voor het eindpunt. Deze zelfstudie maakt gebruik van **ContosoSBQueueEndpoint**.
   
   **Naamruimte servicebus**: Gebruik de vervolgkeuzelijst om de naamruimte van de servicebus te selecteren die u in de voorbereidingsstappen hebt ingesteld. In deze zelfstudie wordt gebruikgemaakt van **ContosoSBNamespace**.

   **Wachtrij servicebus**: gebruik de vervolgkeuzelijst om de wachtrij servicebus te selecteren. In deze zelfstudie wordt gebruikgemaakt van **contososbqueue**.

5. Selecteer **Maken** om het eindpunt van de wachtrijservicebus toe te voegen. U gaat terug naar het deelvenster **Route toevoegen**.

6. Vul nu de rest van de informatie voor de routeringsquery in. Deze query specificeert de criteria voor het verzenden van berichten naar de Service Bus-wachtrij die u zojuist hebt toegevoegd als eindpunt. Vul de velden in op het scherm. 

   **Naam**: voer een naam in voor uw routeringsquery. Deze zelfstudie maakt gebruik van **ContosoSBQueueRoute**. 

   **Eindpunt**: geeft het eindpunt weer dat u net hebt ingesteld.

   **Gegevensbron**: selecteer **Telemetrieberichten apparaat** uit de vervolgkeuzelijst.

   **Routeringsquery**: voer `level="critical"` in als querytekenreeks. 

   ![Een routeringsquery maken voor de wachtrij servicebus](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Selecteer **Opslaan**. Wanneer u teruggaat naar het deelvenster Routes, ziet u de twee nieuwe routeringsregels zoals deze hier zijn weergegeven.

   ![De routes die u zojuist hebt ingesteld](./media/tutorial-routing/message-routing-show-both-routes.png)

8. U de aangepaste eindpunten die u instelt bekijken door het tabblad **Aangepaste eindpunten te** selecteren.

   ![Het aangepaste eindpunt dat u zojuist hebt ingesteld](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Sluit het deelvenster Berichtroutering, waarna u terugkeert naar het deelvenster Resourcegroep.

## <a name="create-a-simulated-device"></a>Een gesimuleerd apparaat maken

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u de resources hebt ingesteld en de berichtroutes hebt geconfigureerd, gaat u door naar de volgende zelfstudie om te leren hoe u berichten naar de IoT-hub verzenden en ze naar de verschillende bestemmingen laten worden doorgestuurd. 

> [!div class="nextstepaction"]
> [Deel 2 - Bekijk de resultaten van de routeringsresultaten van het bericht](tutorial-routing-view-message-routing-results.md)

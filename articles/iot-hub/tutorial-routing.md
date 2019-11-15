---
title: Bericht routering voor Azure IoT Hub configureren met behulp van Azure CLI
description: Configureer bericht routering voor Azure IoT Hub met behulp van de Azure CLI en de Azure Portal
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 38a40d628b883c0e7ada824d47d3fdf3d29caf93
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084374"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Zelf studie: Azure CLI en Azure Portal gebruiken om IoT Hub bericht routering te configureren

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>De Azure CLI gebruiken voor het maken van de basis bronnen

In deze zelf studie wordt gebruikgemaakt van de Azure CLI voor het maken van de basis bronnen en gebruikt de [Azure Portal](https://portal.azure.com) om te laten zien hoe u bericht routering configureert en het virtuele apparaat instelt voor testen.

Kopieer en plak het onderstaande script in Cloud Shell en druk op ENTER. Het script wordt één regel per keer uitgevoerd. Hiermee maakt u de basis resources voor deze zelf studie, met inbegrip van het opslag account, het IoT Hub, de Service Bus naam ruimte en de Service Bus wachtrij.

Er zijn verschillende resource namen die wereld wijd uniek moeten zijn, zoals de naam van de IoT Hub en de naam van het opslag account. Om dit eenvoudiger te maken, worden deze resource namen toegevoegd met een wille keurige alfanumerieke waarde met de naam *randomValue*. De randomValue wordt eenmaal aan het begin van het script gegenereerd en aan de resource namen toegevoegd, zoals nodig is in het script. Als u niet wilt dat deze wille keurig worden ingesteld, kunt u deze instellen op een lege teken reeks of op een specifieke waarde.

> [!TIP]
> Een tip over fout opsporing: dit script maakt gebruik van het vervolg symbool (de back slash `\`) om het script beter leesbaar te maken. Als u een probleem hebt met het uitvoeren van het script, controleert u of uw Cloud Shell-sessie wordt uitgevoerd `bash` en of er geen spaties na een van de backslashes staan.
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

Nu de basis bronnen zijn ingesteld, kunt u de route ring van berichten configureren in de [Azure Portal](https://portal.azure.com).

## <a name="set-up-message-routing"></a>Berichtroutering instellen

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Een opslag account door sturen

Stel nu de routering in voor het opslagaccount. Ga naar het deelvenster Berichtroutering en voeg een route toe. Definieer bij het toevoegen van de route een nieuw eindpunt voor de route. Nadat deze route ring is ingesteld, worden berichten waarvoor de eigenschap **niveau** is ingesteld op **opslag** automatisch naar een opslag account geschreven. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. Selecteer in het [Azure Portal](https://portal.azure.com) **resource groepen**en selecteer vervolgens de resource groep. In deze zelfstudie wordt gebruikgemaakt van **ContosoResources**.

2. Selecteer de IoT-hub onder de lijst met resources. In deze zelfstudie wordt gebruikgemaakt van **ContosoTestHub**.

3. Selecteer **bericht routering**. Selecteer +**toevoegen**in het deel venster **bericht routering** . Selecteer in het deel venster **een route toevoegen** +**toevoegen** naast het veld eind punt om de ondersteunde eind punten weer te geven, zoals wordt weer gegeven in de volgende afbeelding:

   ![Toevoegen van een eind punt voor een route starten](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. Selecteer **Blob-opslag**. U ziet het deel venster **een opslag eindpunt toevoegen** .

   ![Een eind punt toevoegen](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. Voer een naam in voor het eindpunt. In deze zelf studie wordt gebruikgemaakt van **ContosoStorageEndpoint**.

6. Selecteer **een container kiezen**. Er wordt een lijst met opslagaccounts weergegeven. Selecteer het account dat u hebt ingesteld in de voorbereidende stappen. In deze zelfstudie wordt gebruikgemaakt van **contosostorage**. Er wordt een lijst met containers in die opslagaccount weergegeven. **Selecteer** de container die u in de voorbereidings stappen hebt ingesteld. In deze zelfstudie wordt gebruikgemaakt van **contosoresults**. U gaat terug naar het deel venster **een opslag eindpunt toevoegen** en de selecties die u hebt gemaakt.

7. Stel de code ring in op AVRO of JSON. Gebruik ten behoeve van deze zelfstudie de standaardinstellingen voor de rest van de velden. Dit veld wordt grijs weer gegeven als de geselecteerde regio geen ondersteuning biedt voor JSON-code ring.

   > [!NOTE]
   > U kunt de indeling van de blob-naam instellen met de **indeling van blobbestandsnaam**. De standaardwaarde is `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. De indeling moet {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH}, en {mm} bevatten, in willekeurige volgorde.
   >
   > Als u bijvoorbeeld de indeling van de standaardnaam voor het blobbestand gebruikt, de naam van de hub ContosoTestHub is en de datum en tijd 30 oktober 2018 om 10:56 uur, dan ziet de blobnaam er als volgt uit: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > De blobs worden geschreven in de Avro-indeling.
   >

8. Selecteer **maken** om het opslag eindpunt te maken en toe te voegen aan de route. U gaat terug naar het deelvenster **Route toevoegen**.

9. Vul nu de rest van de informatie voor de routeringsquery in. Deze query specificeert de criteria voor het verzenden van berichten naar de opslagcontainer die u zojuist hebt toegevoegd als eindpunt. Vul de velden in op het scherm.

   **Naam**: voer een naam in voor uw routeringsquery. In deze zelf studie wordt gebruikgemaakt van **ContosoStorageRoute**.

   **Eindpunt**: geeft het eindpunt weer dat u net hebt ingesteld.

   **Gegevensbron**: selecteer **Telemetrieberichten apparaat** uit de vervolgkeuzelijst.

   **Route inschakelen**: Zorg ervoor dat dit veld is ingesteld op `enabled`.
   
   **Routeringsquery**: voer `level="storage"` in als querytekenreeks.

   ![Een routerings query maken voor het opslag account](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Selecteer **Opslaan**. Wanneer dit is voltooid, gaat u terug naar het deelvenster Berichtroutering, waar u uw nieuwe routeringsquery voor opslag kunt zien. Sluit het deelvenster Routes, waarna u terugkeert naar de pagina Resourcegroep.

### <a name="route-to-a-service-bus-queue"></a>Een Service Bus wachtrij omleiden

Stel nu de routering in voor de Service Bus-wachtrij. Ga naar het deelvenster Berichtroutering en voeg een route toe. Definieer bij het toevoegen van de route een nieuw eindpunt voor de route. Nadat deze route is ingesteld, worden berichten waarvan de eigenschap **niveau** is ingesteld op **kritiek** , naar de service bus wachtrij geschreven, waarmee een logische app wordt geactiveerd, die vervolgens een e-mail bericht met de informatie verzendt.

1. Selecteer uw IoT-hub op de pagina resource groep en selecteer vervolgens **bericht routering**.

2. Selecteer +**toevoegen**in het deel venster **bericht routering** .

3. Selecteer in het deel venster **een route toevoegen** +**toevoegen** naast het veld eind punt. Selecteer een **Service Bus-wachtrij**. Het deelvenster **Service Bus-eindpunt toevoegen** wordt weergegeven.

   ![Een service bus-eind punt toevoegen](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Vul de velden in:

   **Eindpuntnaam**: voer een naam in voor het eindpunt. In deze zelf studie wordt gebruikgemaakt van **ContosoSBQueueEndpoint**.
   
   **Service Bus naam ruimte**: gebruik de vervolg keuzelijst om de service bus-naam ruimte te selecteren die u in de voorbereidings stappen hebt ingesteld. In deze zelfstudie wordt gebruikgemaakt van **ContosoSBNamespace**.

   **Service Bus wachtrij**: gebruik de vervolg keuzelijst om de service bus wachtrij te selecteren. In deze zelfstudie wordt gebruikgemaakt van **contososbqueue**.

5. Selecteer **maken** om het eind punt van de service bus wachtrij toe te voegen. U gaat terug naar het deelvenster **Route toevoegen**.

6. Vul nu de rest van de informatie voor de routeringsquery in. Deze query specificeert de criteria voor het verzenden van berichten naar de Service Bus-wachtrij die u zojuist hebt toegevoegd als eindpunt. Vul de velden in op het scherm. 

   **Naam**: voer een naam in voor uw routeringsquery. In deze zelf studie wordt gebruikgemaakt van **ContosoSBQueueRoute**. 

   **Eindpunt**: geeft het eindpunt weer dat u net hebt ingesteld.

   **Gegevensbron**: selecteer **Telemetrieberichten apparaat** uit de vervolgkeuzelijst.

   **Routeringsquery**: voer `level="critical"` in als querytekenreeks. 

   ![Een routerings query maken voor de Service Bus wachtrij](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Selecteer **Opslaan**. Wanneer u teruggaat naar het deelvenster Routes, ziet u de twee nieuwe routeringsregels zoals deze hier zijn weergegeven.

   ![De routes die u zojuist hebt ingesteld](./media/tutorial-routing/message-routing-show-both-routes.png)

8. U kunt de aangepaste eind punten die u hebt ingesteld, zien door het tabblad **aangepaste eind punten** te selecteren.

   ![Het aangepaste eind punt dat u zojuist hebt ingesteld](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Sluit het deelvenster Berichtroutering, waarna u terugkeert naar het deelvenster Resourcegroep.

## <a name="create-a-simulated-device"></a>Een gesimuleerd apparaat maken

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u de resources hebt ingesteld en de bericht routes hebt geconfigureerd, gaat u naar de volgende zelf studie om te leren hoe u berichten verzendt naar de IoT-hub en hoe deze worden doorgestuurd naar de verschillende bestemmingen. 

> [!div class="nextstepaction"]
> [Deel 2: de routerings resultaten van het bericht weer geven](tutorial-routing-view-message-routing-results.md)

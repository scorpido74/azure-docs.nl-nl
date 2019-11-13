---
title: Zelf studie-een verrijking van Azure IoT Hub-berichten gebruiken
description: Zelf studie waarin wordt getoond hoe u verrijkingen van berichten gebruikt voor Azure IoT Hub berichten
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 0dd6c410040eea9eb4039ab5da183cc0b6799493
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005786"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments"></a>Zelf studie: verrijkingen van Azure IoT Hub-berichten gebruiken

*Verrijkingen* van berichten is de mogelijkheid van de IOT hub om berichten met aanvullende informatie af te *stem pelen* voordat de berichten naar het aangewezen eind punt worden verzonden. Een reden voor het gebruik van verrijkingen van berichten is het insluiten van gegevens die kunnen worden gebruikt voor het vereenvoudigen van de downstream-verwerking. Het verrijken van telemetriegegevens van een apparaat met een dubbele tag van een apparaat kan bijvoorbeeld de belasting van klanten verminderen om deze informatie te laten opleveren voor Device-dubbele API-aanroepen. Zie het [overzicht van verrijkingen van berichten](iot-hub-message-enrichments-overview.md)voor meer informatie.

In deze zelf studie gebruikt u de Azure CLI om de resources in te stellen, waaronder twee eind punten die naar twee verschillende opslag containers verwijzen: **verrijkt** en **Origineel**. Vervolgens gebruikt u de [Azure Portal](https://portal.azure.com) voor het configureren van verrijkingen van berichten die alleen worden toegepast op berichten die naar het eind punt worden verzonden met de **verrijkte** opslag container. U stuurt berichten naar de IoT Hub, die worden doorgestuurd naar beide opslag containers. Alleen de berichten die naar het eind punt voor de **verrijkte** opslag container worden verzonden, worden verrijkt.

Hier volgen de taken die u moet uitvoeren om deze zelf studie te volt ooien:

**IoT Hub-bericht verrijkingen gebruiken**
> [!div class="checklist"]
> * Maak met behulp van de Azure CLI de resources--een IoT-hub, een opslag account met twee eind punten en de routerings configuratie.
> * Gebruik de Azure Portal voor het configureren van verrijkingen van berichten.
> * Voer een app uit die een IoT-apparaat simuleert die berichten naar de hub verzendt.
> * Bekijk de resultaten en controleer of de verrijkingen van het bericht werken zoals verwacht.

## <a name="prerequisites"></a>Vereisten

* U hebt een abonnement op Azure nodig. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

* Installeer [Visual Studio](https://www.visualstudio.com/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>De voorbeeld code ophalen

Down load [IOT Device simulatie](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) en pak deze uit. Deze opslag plaats bevat meerdere toepassingen, waaronder het account dat u gaat gebruiken om berichten te verzenden naar de IoT-hub.

Deze down load bevat ook het script voor het maken van de resources die worden gebruikt voor het testen van de verrijkingen van berichten. Het script bevindt zich in/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli. azcli. U kunt het script nu bekijken en gebruiken. U kunt het script ook rechtstreeks vanuit het artikel kopiëren.

Wanneer u klaar bent om te testen, gebruikt u de toepassing Device simulatie van deze down load om een bericht te verzenden naar uw IoT-hub.

## <a name="set-up-and-configure-resources"></a>Bronnen instellen en configureren

Naast het maken van de benodigde resources, configureert het Azure CLI-script ook de twee routes naar de eind punten die afzonderlijke opslag containers zijn. Zie de [zelf studie voor route ring](tutorial-routing.md)voor meer informatie over het configureren van de route ring. Nadat de resources zijn ingesteld, gebruikt u de [Azure Portal](https://portal.azure.com) voor het configureren van verrijkingen van berichten voor elk eind punt en gaat u verder met de test stap.

> [!NOTE]
> Alle berichten worden doorgestuurd naar beide eind punten, maar alleen de berichten naar het eind punt met geconfigureerde bericht verrijkingen worden uitgebreid.
>

U kunt het onderstaande script gebruiken of het script openen in de map/resources van de gedownloade opslag plaats. Dit zijn de stappen die door het script worden uitgevoerd:

* Een IoT Hub maken.
* Een opslagaccount maken.
* Maak twee containers in het opslag account: een voor de verrijkte berichten en een voor berichten die niet zijn verrijkt.
* Route ring voor de twee verschillende opslag accounts instellen.
    * Maak een eind punt voor elke container van het opslag account.
    * Maak een route naar elk van de container-eind punten van het opslag account.

Er zijn verschillende resource namen die wereld wijd uniek moeten zijn, zoals de naam van de IoT Hub en de naam van het opslag account. Om het script eenvoudiger te kunnen uitvoeren, worden deze resource namen toegevoegd met een wille keurige alfanumerieke waarde met de naam *randomValue*. De randomValue wordt eenmaal aan het begin van het script gegenereerd en aan de resource namen toegevoegd, zoals nodig is in het script. Als u niet wilt dat deze wille keurig worden ingesteld, kunt u deze instellen op een lege teken reeks of op een specifieke waarde.

Als u dit nog niet hebt gedaan, opent u een [Cloud shell-venster](https://shell.azure.com) en zorgt u ervoor dat het is ingesteld op bash. Open het script in de uitgepakte opslag plaats, druk op CTRL-A om alles te selecteren en druk vervolgens op CTRL + C om de map te kopiëren. U kunt ook het volgende CLI-script kopiëren of het rechtstreeks openen in Cloud Shell. Plak het script in het venster Cloud Shell door met de rechter muisknop op de opdracht regel te klikken en **Plakken**te selecteren. Het script wordt één instructie per keer uitgevoerd. Nadat het script is uitgevoerd, selecteert u **Enter** om te controleren of de laatste opdracht wordt uitgevoerd. In het volgende code blok ziet u het script dat wordt gebruikt, met opmerkingen waarin wordt uitgelegd wat er gebeurt.

Dit zijn de resources die zijn gemaakt door het script. **Verrijkt** betekent dat de resource is bedoeld voor berichten met verrijkingen. **Oorspronkelijk** betekent dat de resource is bedoeld voor berichten die niet zijn verrijkt.

| Naam | Waarde |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| container naam | Origineel  |
| container naam | geavanceerde  |
| IoT-apparaatnaam | Contoso-Test-Device |
| IoT Hub naam | ContosoTestHubMsgEn |
| Naam van opslag account | contosostorage |
| eindpunt naam 1 | ContosoStorageEndpointOriginal |
| Naam van eind punt 2 | ContosoStorageEndpointEnriched|
| route naam 1 | ContosoStorageRouteOriginal |
| route naam 2 | ContosoStorageRouteEnriched |

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
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
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
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for the first container, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for the second container, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

Op dit moment worden de resources allemaal ingesteld en wordt de route ring geconfigureerd. U kunt de configuratie voor bericht routering bekijken in de portal en de bericht verrijkingen instellen voor berichten die worden verzonden naar de **verrijkde** opslag container.

### <a name="view-routing-and-configure-the-message-enrichments"></a>Route ring weer geven en de bericht verrijkingen configureren

1. Ga naar uw IoT Hub door **resource groepen**te selecteren en selecteer vervolgens de resource groep die u voor deze zelf studie hebt ingesteld (**ContosoResources_MsgEn**). Zoek de IoT Hub in de lijst en selecteer deze. Selecteer **bericht routering** voor de IOT-hub.

   ![Bericht routering selecteren](./media/tutorial-message-enrichments/select-iot-hub.png)

   Het deel venster bericht routering bevat drie tabbladen: **routes**, **aangepaste eind punten**en **verrijkte berichten**. U kunt door de eerste twee tabbladen bladeren om de configuratie weer te geven die door het script is ingesteld. Gebruik het derde tabblad om bericht verrijkingen toe te voegen. Laten we berichten verrijken naar het eind punt voor de opslag container met de naam **verrijkt**. Vul de naam en waarde in en selecteer het eind punt **ContosoStorageEndpointEnriched** in de vervolg keuzelijst. Hier volgt een voor beeld van het instellen van een verrijking waarmee de IoT Hub naam wordt toegevoegd aan het bericht:

   ![Eerste verrijking toevoegen](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Voeg deze waarden toe aan de lijst voor het ContosoStorageEndpointEnriched-eind punt.

   | Sleutel | Waarde | Eind punt (vervolg keuzelijst) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | deviceLocation | $twin. Tags. locatie | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |Nummer | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Als uw apparaat geen dubbele waarde heeft, wordt de door u in deze plaats in te voegen stempel als teken reeks weer gegeven voor de waarde in de verrijkingen van het bericht. Als u de dubbele gegevens van het apparaat wilt zien, gaat u naar uw hub in de portal, selecteert u **IOT-apparaten**, selecteert u uw apparaat en selecteert u vervolgens **apparaat-twee** boven aan de pagina.
   >
   > U kunt de dubbele informatie bewerken om tags toe te voegen (zoals locatie) en deze zo in te stellen op een specifieke waarde als u wilt. Zie [Understand and use device twins in IoT Hub](iot-hub-devguide-device-twins.md) (Apparaatdubbelen begrijpen en gebruiken in IoT Hub) voor meer informatie

3. Wanneer u klaar bent, moet het deel venster er ongeveer als volgt uitzien:

   ![Tabel met alle verrijkingen toegevoegd](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Selecteer **Toep assen** om de wijzigingen op te slaan.

## <a name="send-messages-to-the-iot-hub"></a>Berichten verzenden naar de IoT Hub

Nu de verrijkingen van het bericht zijn geconfigureerd voor het eind punt, voert u de toepassing voor gesimuleerde apparaten uit om berichten naar de IoT Hub te verzenden. De hub is ingesteld met instellingen die de volgende taken uitvoeren:

* Berichten die naar de ContosoStorageEndpointOriginal van het opslag eindpunt worden doorgestuurd, worden niet verrijkt en worden opgeslagen in de opslag container `original`.

* Berichten die naar de ContosoStorageEndpointEnriched van het opslag eindpunt worden doorgestuurd, worden verrijkt en opgeslagen in de opslag container `enriched`.

De toepassing van het gesimuleerde apparaat is een van de toepassingen in de uitgepakte down load. De toepassing verzendt berichten voor elk van de verschillende routerings methoden voor berichten in de [zelf studie voor route ring](tutorial-routing.md). Dit omvat Azure Storage.

Dubbel klik op het oplossings bestand (IoT_SimulatedDevice. SLN) om de code te openen in Visual Studio en open vervolgens Program.cs. Vervang de naam van de IoT-hub door de `{your hub name}`voor de markering. De indeling van de naam van de IoT-hub is **{uw naam van de hub}. Azure-devices.net**. Voor deze zelf studie is de hostnaam van de hub **ContosoTestHubMsgEn.Azure-devices.net**. Vervang vervolgens de apparaatnaam die u eerder hebt opgeslagen tijdens het uitvoeren van het script om de resources voor het Markeer `{your device key}`te maken.

Als u de apparaatcode niet hebt, kunt u deze ophalen uit de portal. Nadat u zich hebt aangemeld, gaat u naar **resource groepen**, selecteert u de resource groep en selecteert u uw IOT hub. Zoek in **IOT-apparaten** voor uw test apparaat en selecteer uw apparaat. Selecteer het Kopieer pictogram naast **primaire sleutel** om het naar het klem bord te kopiëren.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

## <a name="run-and-test"></a>Uitvoeren en testen

Voer de consoletoepassing uit. Wacht enkele minuten. De berichten die worden verzonden, worden weer gegeven in het console scherm van de toepassing.

Deze app verzendt elke seconde een nieuw apparaat-naar-cloud-bericht naar de IoT Hub. Het bericht bevat een JSON-geserialiseerd object met een apparaat-id, temperatuur, vochtigheid en berichtniveau, die als standaardwaarde `normal` heeft. Er wordt wille keurig een niveau van `critical` of `storage`toegewezen, waardoor het bericht wordt doorgestuurd naar het opslag account of het standaard eindpunt. De berichten die worden verzonden naar de uitgebreide container in het opslag account, worden **verrijkt** .

Nadat er meerdere opslag berichten zijn verzonden, bekijkt u de gegevens.

1. Selecteer **resource groepen**, zoek de resource groep (ContosoResourcesMsgEn) en selecteer deze.

2. Selecteer uw opslag account (contosostorage). Selecteer vervolgens **Storage Explorer (preview)** in het selectie deel venster aan de linkerkant.

   ![Opslag Verkenner selecteren](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Selecteer **BLOB-containers** om de twee containers te zien die kunnen worden gebruikt.

   ![Zie de containers in het opslag account](./media/tutorial-message-enrichments/show-blob-containers.png)

De berichten in de container met de naam **verrijkt** de bericht verrijkingen die in de berichten zijn opgenomen. De berichten in de container **Origineel** hebben de onbewerkte berichten zonder verrijkingen. Zoom in op een van de containers totdat u het meest recente bericht bestand opent en open vervolgens de andere container om te controleren of er geen verrijkingen worden toegevoegd aan berichten in die container.

Wanneer u kijkt naar berichten die zijn verrijkt, ziet u de ' mijn IoT Hub ' met de naam van de hub, evenals de locatie en de klant-ID, zoals:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Dit is een niet-verrijkt bericht. ' mijn IoT Hub ', ' devicelocation ' en ' KlantId ' worden hier niet weer gegeven, omdat dit de velden zijn die door de verrijkingen zouden worden toegevoegd en dit eind punt geen verrijkingen heeft.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle resources wilt verwijderen die u in deze zelf studie hebt gemaakt, verwijdert u de resource groep. Hiermee verwijdert u ook alle resources in de groep. In dit geval worden de IoT-hub, het opslagaccount en de resourcegroep zelf verwijderd.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>De Azure CLI gebruiken om resources op te schonen

U kunt de resourcegroep verwijderen met de opdracht [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` is ingesteld op **ContosoResources** terug aan het begin van deze zelf studie.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geconfigureerd en getest hoe u verrijkingen van berichten kunt toevoegen aan IoT Hub berichten met de volgende stappen:

**IoT Hub-bericht verrijkingen gebruiken**
> [!div class="checklist"]
> * Maak met behulp van de Azure CLI de resources--een IoT-hub, een opslag account met twee enendpoints en de routerings configuratie.
> * Gebruik de Azure Portal voor het configureren van verrijkingen van berichten.
> * Voer een app uit die een IoT-apparaat simuleert dat een bericht verzendt naar de hub.
> * Bekijk de resultaten en controleer of de verrijkingen van het bericht werken zoals verwacht.

Zie voor meer informatie over het verrijken van berichten het [overzicht van verrijkingen van berichten](iot-hub-message-enrichments-overview.md).

Zie de volgende artikelen voor meer informatie over het routeren van berichten:

* [IoT Hub bericht routering gebruiken om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten](iot-hub-devguide-messages-d2c.md)

* [Zelf studie: IoT Hub route ring](tutorial-routing.md)
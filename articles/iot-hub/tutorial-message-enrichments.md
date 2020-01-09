---
title: Zelf studie-een verrijking van Azure IoT Hub-berichten gebruiken
description: Zelf studie waarin wordt getoond hoe u verrijkingen van berichten gebruikt voor Azure IoT Hub berichten
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.openlocfilehash: 323730fff4659c87058669016b69808a880994cf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453833"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments"></a>Zelf studie: verrijkingen van Azure IoT Hub-berichten gebruiken

*Verrijkingen* van berichten is de mogelijkheid van de IOT hub om berichten met aanvullende informatie af te *stem pelen* voordat de berichten naar het aangewezen eind punt worden verzonden. Een reden voor het gebruik van verrijkingen van berichten is het insluiten van gegevens die kunnen worden gebruikt voor het vereenvoudigen van de downstream-verwerking. Het verrijken van telemetriegegevens van een apparaat met een dubbele tag van een apparaat kan bijvoorbeeld de belasting van klanten verminderen om deze informatie te laten opleveren voor Device-dubbele API-aanroepen. Zie het [overzicht van verrijkingen van berichten](iot-hub-message-enrichments-overview.md)voor meer informatie.

In deze zelf studie ziet u twee manieren om de resources te maken en te configureren die nodig zijn om de verrijkingen van het bericht voor een IoT Hub te testen. De resources bevatten één opslag account met twee opslag containers: een voor de verrijkte berichten en één voor de oorspronkelijke berichten. Ook is een IoT-hub opgenomen om de berichten te ontvangen en deze te routeren naar de juiste opslag container, afhankelijk van het feit of ze zijn verrijkt of niet. 

* De eerste methode is het gebruik van de Azure CLI om de resources te maken en de bericht routering te configureren. Vervolgens definieert u de verrijkingen hand matig met behulp van de [Azure Portal](https://portal.azure.com). 

* De tweede methode is het gebruik van een Azure Resource Manager sjabloon voor het maken van de resources *en* de configuraties voor de bericht Routering en het verrijkings bericht van berichten. 

Nadat de configuraties voor de bericht Routering en de verrijkingen van berichten zijn voltooid, gebruikt u een toepassing om berichten naar de IoT Hub te verzenden, die vervolgens naar beide opslag containers worden doorgestuurd. Alleen de berichten die worden verzonden naar het eind punt voor de **verrijkte** opslag container, worden uitgebreid.

Hier volgen de taken die u uitvoert om deze zelf studie te volt ooien:

**IoT Hub-bericht verrijkingen gebruiken**
> [!div class="checklist"]
> * Eerste methode: verrijkingen hand matig bericht
>   - Maak bronnen en configureer bericht routering met behulp van de Azure CLI.
>   - Configureer de verrijkingen van het bericht hand matig met behulp van de [Azure Portal](https://portal.azure.com).
> * Tweede methode: een RM-sjabloon gebruiken
>   - Maak resources, Configureer bericht Routering en verrijkingen van berichten met behulp van een Azure Resource Manager sjabloon. 
> * Voer een app uit die een IoT-apparaat simuleert die berichten naar de hub verzendt.
> * Bekijk de resultaten en controleer of de verrijkingen van het bericht werken zoals verwacht.

## <a name="prerequisites"></a>Vereisten

* U hebt een abonnement op Azure nodig. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

* Installeer [Visual Studio](https://www.visualstudio.com/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>De opslag plaats C# voor de IOT-voor beelden ophalen

Down load [de C# IOT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) -voor beelden van github en pak deze uit. Deze opslag plaats bevat verschillende toepassingen, scripts en Resource Manager-sjablonen. De items die voor deze zelf studie moeten worden gebruikt, zijn als volgt:

* Voor de hand matige methode is er een CLI-script voor het maken van de resources. Dit script bevindt zich in **/azure-iot-samples-csharp/iot-hub/tutorials/routing/simulateddevice/resources/iothub_msgenrichment_cli. azcli**. Met dit script worden de resources gemaakt en wordt de bericht routering geconfigureerd. Nadat u dit hebt uitgevoerd, maakt u de verrijkingen van het bericht hand matig met behulp van de [Azure Portal](https://portal.azure.com) en voert u vervolgens de DeviceSimulation-app uit om te zien welke verrijkingen werken.

* Voor de geautomatiseerde methode is er een Azure Resource Manager sjabloon. De sjabloon bevindt zich in **/azure-iot-samples-csharp/iot-hub/tutorials/routing/simulateddevice/resources/template_msgenrichments. json**. Met deze sjabloon maakt u de resources, configureert u de bericht Routering en configureert u ten slotte de verrijkingen van het bericht. Nadat u deze sjabloon hebt geladen, voert u de app voor het simuleren van apparaten uit om te zien welke verrijkingen werken.

* De derde toepassing die u gebruikt, is de app voor het simuleren van apparaten, die u gebruikt om berichten te verzenden naar de IoT-hub en de verrijkingen van het bericht te testen.

## <a name="manual-set-up-and-configuration-using-azure-cli"></a>Hand matige installatie en configuratie met behulp van Azure CLI

Naast het maken van de benodigde resources, configureert het Azure CLI-script ook de twee routes naar de eind punten die afzonderlijke opslag containers zijn. Voor meer informatie over het configureren van de bericht routering raadpleegt u de [zelf studie voor route ring](tutorial-routing.md). Nadat de resources zijn ingesteld, gebruikt u de [Azure Portal](https://portal.azure.com) voor het configureren van verrijkingen van berichten voor elk eind punt en gaat u verder met de test stap.

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

Als u dit nog niet hebt gedaan, opent u een [Cloud shell-venster](https://shell.azure.com) en zorgt u ervoor dat het is ingesteld op bash. Open het script in de uitgepakte opslag plaats, druk op CTRL-A om alles te selecteren en druk vervolgens op CTRL + C om de map te kopiëren. U kunt ook het volgende CLI-script kopiëren of het rechtstreeks openen in Cloud Shell. Plak het script in het venster Cloud Shell door met de rechter muisknop op de opdracht regel te klikken en **Plakken**te selecteren. Het script voert één instructie per keer uit. Nadat het script is uitgevoerd, selecteert u **Enter** om te controleren of de laatste opdracht wordt uitgevoerd. In het volgende code blok ziet u het script dat wordt gebruikt, met opmerkingen waarin wordt uitgelegd wat er gebeurt.

Dit zijn de resources die zijn gemaakt door het script. **Verrijkt** betekent dat de resource is bedoeld voor berichten met verrijkingen. **Oorspronkelijk** betekent dat de resource is bedoeld voor berichten die niet zijn verrijkt.

| Name | Waarde |
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

Op dit moment worden de resources allemaal ingesteld en wordt de bericht routering geconfigureerd. U kunt de configuratie voor bericht routering bekijken in de portal en de bericht verrijkingen instellen voor berichten die worden verzonden naar de **verrijkde** opslag container.

### <a name="manually-configure-the-message-enrichments-using-the-azure-portal"></a>Configureer de bericht verrijkingen hand matig met behulp van de Azure Portal

1. Ga naar uw IoT Hub door **resource groepen**te selecteren en selecteer vervolgens de resource groep die u voor deze zelf studie hebt ingesteld (**ContosoResourcesMsgEn**). Zoek de IoT Hub in de lijst en selecteer deze. Selecteer **bericht routering** voor de IOT-hub.

   ![Bericht routering selecteren](./media/tutorial-message-enrichments/select-iot-hub.png)

   Het deel venster bericht routering bevat drie tabbladen: **routes**, **aangepaste eind punten**en **verrijkte berichten**. U kunt door de eerste twee tabbladen bladeren om de configuratie weer te geven die door het script is ingesteld. Gebruik het derde tabblad om bericht verrijkingen toe te voegen. Laten we berichten verrijken naar het eind punt voor de opslag container met de naam **verrijkt**. Vul de naam en waarde in en selecteer het eind punt **ContosoStorageEndpointEnriched** in de vervolg keuzelijst. Hier volgt een voor beeld van het instellen van een verrijking waarmee de IoT Hub naam wordt toegevoegd aan het bericht:

   ![Eerste verrijking toevoegen](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Voeg deze waarden toe aan de lijst voor het ContosoStorageEndpointEnriched-eind punt.

   | Sleutel | Waarde | Eind punt (vervolg keuzelijst) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin. Tags. locatie | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |Nummer | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Als uw apparaat geen dubbele waarde heeft, wordt de door u in deze plaats in te voegen stempel als teken reeks weer gegeven voor de waarde in de verrijkingen van het bericht. Als u de dubbele gegevens van het apparaat wilt zien, gaat u naar uw hub in de portal, selecteert u **IOT-apparaten**, selecteert u uw apparaat en selecteert u vervolgens **apparaat-twee** boven aan de pagina.
   >
   > U kunt de dubbele informatie bewerken om tags toe te voegen (zoals locatie) en deze zo in te stellen op een specifieke waarde als u wilt. Zie [Understand and use device twins in IoT Hub](iot-hub-devguide-device-twins.md) (Apparaatdubbelen begrijpen en gebruiken in IoT Hub) voor meer informatie

3. Wanneer u klaar bent, moet het deel venster er ongeveer als volgt uitzien:

   ![Tabel met alle verrijkingen toegevoegd](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Selecteer **Toep assen** om de wijzigingen op te slaan. Ga verder naar de sectie [verrijkingen](#testing-message-enrichments) van het testen van berichten.

## <a name="use-an-rm-template-to-create-and-configure-the-resources-message-routing-and-message-enrichments"></a>Een RM-sjabloon gebruiken voor het maken en configureren van de resources, bericht Routering en bericht verrijkingen 

1. Meld u aan bij de Azure-portal. Klik op **+ een resource maken**. Hiermee wordt het zoekvak geopend. Zoek naar de **sjabloon implementatie**. Selecteer in het deel venster met resultaten **Sjabloonimlementatie (implementeren met aangepaste sjabloon)** .

   ![Sjabloonimlementatie in de Azure Portal](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Selecteer **maken** in het deel venster sjabloon implementatie. 

1. In het deel venster aangepaste implementatie. Selecteer **uw eigen sjabloon bouwen in de editor**.

1. Selecteer in het deel venster sjabloon bewerken de optie **bestand laden**. U ziet de Windows Verkenner. Zoek het bestand **template_messageenrichments. json** in het uitgepakte bestand opslag plaats in **/IOT-hub/tutorials/Routing/SimulatedDevice/resources**. 

   ![Sjabloon selecteren op de lokale computer](./media/tutorial-message-enrichments/template-select.png)

1. Selecteer **openen** om het sjabloon bestand vanaf de lokale machine te laden. Het wordt geladen in het bewerkings venster en wordt weer gegeven.

   Deze sjabloon is ingesteld voor het gebruik van een globaal unieke IoT Hub naam en opslag accountnaam door een wille keurige waarde toe te voegen aan het einde van de standaard namen, zodat u de sjabloon kunt gebruiken zonder wijzigingen aan te brengen. 

   Hier vindt u de resources die zijn gemaakt door het laden van de sjabloon. **Verrijkt** betekent dat de resource is bedoeld voor berichten met verrijkingen. **Oorspronkelijk** betekent dat de resource is bedoeld voor berichten die niet zijn verrijkt. Dit zijn dezelfde waarden die worden gebruikt in het Azure CLI-script.

   | Name | Waarde |
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

1. Selecteer **Opslaan**en het deel venster aangepaste implementatie wordt weer gegeven, met alle para meters die worden gebruikt door de sjabloon. Het enige veld dat u moet instellen is de **resource groep**. Maak een nieuw item of selecteer er een in de vervolg keuzelijst.

   Hier volgt de bovenste helft van het aangepaste implementatie deel venster. U kunt zien waar u de resource groep invult.

   ![Bovenste helft van het deel venster voor aangepaste implementatie](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Hier volgt de onderste helft van het aangepaste implementatie deel venster. U kunt de rest van de para meters en de voor waarden zien. 

   ![Onderste helft van het deel venster voor aangepaste implementatie](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Schakel het selectie vakje in om aan te geven dat u akkoord gaat met de voor waarden en selecteer vervolgens **aankoop** om door te gaan met de implementatie van de sjabloon.

1. Wacht tot de sjabloon volledig is geïmplementeerd. U kunt het klok pictogram boven aan het scherm selecteren om de voortgang te controleren. Wanneer het proces is voltooid, kunt u door gaan met het testen van de [bericht verrijkingen](#testing-message-enrichments).

## <a name="testing-message-enrichments"></a>Verrijkingen van berichten testen

U kunt de verrijkingen van het bericht weer geven door **resource groepen**te selecteren en vervolgens de resource groep te selecteren die u voor deze zelf studie gebruikt. Selecteer vervolgens de IoT Hub in de lijst met resources en ga naar **berichten**. Hier ziet u de configuratie van de bericht Routering en de geconfigureerde verrijkingen.

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

### <a name="run-and-test"></a>Uitvoeren en testen

Voer de console toepassing enkele minuten uit. De berichten die worden verzonden, worden weer gegeven in het console scherm van de toepassing.

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

U kunt de resourcegroep verwijderen met de opdracht [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` is ingesteld op **ContosoResourcesMsgEn** terug aan het begin van deze zelf studie.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geconfigureerd en getest hoe u verrijkingen van berichten kunt toevoegen aan IoT Hub berichten met de volgende stappen:

**IoT Hub-bericht verrijkingen gebruiken**
> [!div class="checklist"]
> * Eerste methode
>   * Maak bronnen en configureer bericht routering met behulp van de Azure CLI.
>   * Configureer de verrijkingen van het bericht hand matig met behulp van de [Azure Portal](https://portal.azure.com).
> * Tweede methode
>   * Maak resources, Configureer bericht Routering en verrijkingen van berichten met behulp van een Azure Resource Manager sjabloon. 
> * Voer een app uit die een IoT-apparaat simuleert die berichten naar de hub verzendt.
> * Bekijk de resultaten en controleer of de verrijkingen van het bericht werken zoals verwacht.

Zie voor meer informatie over het verrijken van berichten het [overzicht van verrijkingen van berichten](iot-hub-message-enrichments-overview.md).

Zie de volgende artikelen voor meer informatie over het routeren van berichten:

* [IoT Hub bericht routering gebruiken om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten](iot-hub-devguide-messages-d2c.md)

* [Zelf studie: IoT Hub route ring](tutorial-routing.md)
---
title: Zelfstudie - Azure IoT Hub-berichtverrijkingen gebruiken
description: Zelfstudie met informatie over het gebruik van berichtverrijkingen voor Azure IoT Hub-berichten
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 78aee7829e58feede3360f30f10260903713c52f
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770085"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Zelfstudie: Azure IoT Hub-berichtverrijkingen gebruiken

*Berichtverrijkingen* beschrijven de mogelijkheid van Azure IoT Hub om berichten met aanvullende informatie te *stempelen* voordat de berichten naar het aangewezen eindpunt worden verzonden. Een van de redenen om berichtverrijkingen te gebruiken, is het opnemen van gegevens die kunnen worden gebruikt om downstreamverwerking te vereenvoudigen. Als u bijvoorbeeld telemetrieberichten van apparaten verrijkt met een dubbele apparaattag, kan de belasting van klanten worden verminderd om apparaatdubbele API-oproepen voor deze informatie te maken. Zie [Overzicht van berichtverrijkingen](iot-hub-message-enrichments-overview.md)voor meer informatie .

In deze zelfstudie ziet u twee manieren om de resources te maken en te configureren die nodig zijn om de berichtverrijkingen voor een IoT-hub te testen. De resources bevatten één opslagaccount met twee opslagcontainers. Een container bevat de verrijkte berichten en een andere container bevat de oorspronkelijke berichten. Ook inbegrepen is een IoT-hub om de berichten te ontvangen en ze door te sturen naar de juiste opslagcontainer op basis van of ze zijn verrijkt of niet.

* De eerste methode is om de Azure CLI te gebruiken om de resources te maken en de berichtroutering te configureren. Vervolgens definieert u de verrijkingen handmatig met behulp van de [Azure-portal.](https://portal.azure.com)

* De tweede methode is het gebruik van een Azure Resource Manager-sjabloon om zowel de *resources* als de configuraties voor de berichtrouterings- en berichtverrijkingen te maken.

Nadat de configuraties voor de berichtroutering en berichtverrijkingen zijn voltooid, gebruikt u een toepassing om berichten naar de IoT-hub te verzenden. De hub leidt ze vervolgens naar beide opslagcontainers. Alleen de berichten die naar het eindpunt voor de **verrijkte** opslagcontainer worden verzonden, worden verrijkt.

Dit zijn de taken die u uitvoert om deze zelfstudie te voltooien:

**Verrijkingen van IoT Hub-berichten gebruiken**
> [!div class="checklist"]
> * Eerste methode: Maak resources en configureer berichtroutering met behulp van de Azure CLI. Configureer de berichtverrijkingen handmatig met behulp van de [Azure-portal.](https://portal.azure.com)
> * Tweede methode: Maak resources en configureer berichtrouteringen en berichtverrijkingen met behulp van een resourcemanagersjabloon. 
> * Voer een app uit die een IoT-apparaat simuleert en berichten naar de hub verzendt.
> * Bekijk de resultaten en controleer of de verrijkingen van berichten werken zoals verwacht.

## <a name="prerequisites"></a>Vereisten

* U hebt een abonnement op Azure nodig. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
* Installeer [Visual Studio](https://www.visualstudio.com/).

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in deze zelfstudie maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>De repository van IoT C#-voorbeelden ophalen

Download de [IoT C#-voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) van GitHub en rits ze uit. Deze repository heeft verschillende toepassingen, scripts en Resource Manager-sjablonen. Degenen die moeten worden gebruikt voor deze tutorial zijn als volgt:

* Voor de handmatige methode is er een CLI-script dat wordt gebruikt om de resources te maken. Dit script bevindt zich in /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Met dit script worden de resources gemaakt en wordt de berichtroutering geconfigureerd. Nadat u dit script hebt uitgevoerd, maakt u de berichtverrijkingen handmatig met behulp van de [Azure-portal.](https://portal.azure.com)
* Voor de geautomatiseerde methode is er een Azure Resource Manager-sjabloon. De sjabloon bevindt zich in /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.json. Met deze sjabloon worden de resources gemaakt, wordt de berichtroutering geconfigureerd en worden de berichtverrijkingen geconfigureerd.
* De derde toepassing die u gebruikt is de app Apparaatsimulatie, die u gebruikt om berichten naar de IoT-hub te verzenden en de verrijkingen van het bericht te testen.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Handmatig instellen en configureren met behulp van azure cli

Naast het maken van de benodigde resources configureert het Azure CLI-script ook de twee routes naar de eindpunten die afzonderlijke opslagcontainers zijn. Zie de [zelfstudie Routering](tutorial-routing.md)voor meer informatie over het configureren van de berichtroutering. Nadat de resources zijn ingesteld, gebruikt u de [Azure-portal](https://portal.azure.com) om berichtverrijkingen voor elk eindpunt te configureren. Ga dan verder naar de teststap.

> [!NOTE]
> Alle berichten worden doorgestuurd naar beide eindpunten, maar alleen de berichten die naar het eindpunt gaan met geconfigureerde berichtverrijkingen worden verrijkt.
>

U het script dat volgt gebruiken, of u het script openen in de map /resources van de gedownloade opslagplaats. Met het script voert u de volgende stappen uit:

* Maak een IoT-hub.
* Een opslagaccount maken.
* Maak twee containers in het opslagaccount. Een container is voor de verrijkte berichten, en een andere container is voor berichten die niet zijn verrijkt.
* Routering instellen voor de twee verschillende opslagaccounts:
    * Maak een eindpunt voor elke opslagaccountcontainer.
    * Maak een route naar elk van de eindpunten van de opslagaccountcontainer.

Er zijn verschillende resourcenamen die wereldwijd uniek moeten zijn, zoals de naam van de IoT-hub en de naam van het opslagaccount. Om het uitvoeren van het script eenvoudiger te maken, worden deze resourcenamen toegevoegd met een willekeurige alfanumerieke waarde genaamd *randomValue*. De willekeurige waarde wordt eenmaal gegenereerd aan de bovenkant van het script. Het is toegevoegd aan de resource namen als dat nodig is in het hele script. Als u niet wilt dat de waarde willekeurig is, u deze instellen op een lege tekenreeks of op een specifieke waarde.

Als u dit nog niet hebt gedaan, opent u een Azure [Cloud Shell-venster](https://shell.azure.com) en zorgt u ervoor dat het is ingesteld op Bash. Open het script in de uitgepakte opslagplaats, selecteer Ctrl+A om alles te selecteren en selecteer Ctrl+C om het te kopiëren. U ook het volgende CLI-script kopiëren of rechtstreeks openen in Cloud Shell. Plak het script in het venster Cloud Shell door met de rechtermuisknop op de opdrachtregel te klikken en **Plakken te**selecteren . Het script wordt één verklaring tegelijk uitgevoerd. Nadat het script is gestopt met werken, selecteert u **Enter** om te zien of de laatste opdracht wordt uitgevoerd. Het volgende codeblok toont het script dat wordt gebruikt, met opmerkingen die uitleggen wat het doet.

Dit zijn de bronnen die door het script zijn gemaakt. *Verrijkt* betekent dat de bron is voor berichten met verrijkingen. *Origineel* betekent dat de bron is voor berichten die niet zijn verrijkt.

| Naam | Waarde |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| containernaam | Origineel  |
| containernaam | Verrijkt  |
| Naam van IoT-apparaat | Contoso-Test-Apparaat |
| Naam IoT-hub | ContosoTestHubMsgEn |
| naam van het opslagaccount | contosostorage |
| eindpuntnaam 1 | ContosoStorageEndpointOrigineel |
| eindpuntnaam 2 | ContosoStorageEndpointVerrijkt|
| route naam 1 | ContosoStorageRouteOrigineel |
| route naam 2 | ContosoStorageRouteVerrijkt |

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

Op dit moment zijn de resources allemaal ingesteld en is de berichtroutering geconfigureerd. U de configuratie van de berichtroutering in de portal bekijken en de berichtverrijkingen instellen voor berichten die naar de **verrijkte** opslagcontainer gaan.

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>De berichtverrijkingen handmatig configureren met behulp van de Azure-portal

1. Ga naar uw IoT-hub door **Brongroepen te**selecteren. Selecteer vervolgens de resourcegroep die is ingesteld voor deze zelfstudie **(ContosoResourcesMsgEn).** Zoek de IoT-hub in de lijst en selecteer deze. Selecteer **Berichtroutering** voor de IoT-hub.

   ![Berichtroutering selecteren](./media/tutorial-message-enrichments/select-iot-hub.png)

   Het berichtrouteringsvenster heeft drie tabbladen met het label **Routes,** **Aangepaste eindpunten**en **Berichten verrijken**. Blader door de eerste twee tabbladen om de configuratie te zien die door het script is ingesteld. Gebruik het derde tabblad om berichtverrijkingen toe te voegen. Laten we berichten verrijken naar het eindpunt voor de opslagcontainer met de naam **verrijkt**. Vul de naam en waarde in en selecteer vervolgens het eindpunt **ContosoStorageEndpointEnriched** in de vervolgkeuzelijst. Hier vindt u een voorbeeld van het instellen van een verrijking die de naam van de IoT-hub aan het bericht toevoegt:

   ![Eerste verrijking toevoegen](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Voeg deze waarden toe aan de lijst voor het Eindpunt ContosoStorageEndpointVerrijkt.

   | Sleutel | Waarde | Eindpunt (vervolgkeuzelijst) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | Locatie van apparaten | $twin.tags.locatie | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |Klantid | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Als uw apparaat geen tweeling heeft, wordt de waarde die u hier invoert, gestempeld als een tekenreeks voor de waarde in de berichtverrijkingen. Als u de dubbele informatie van het apparaat wilt zien, gaat u naar uw hub in de portal en selecteert u **IoT-apparaten.** Selecteer uw apparaat en selecteer **vervolgens Apparaattweeling** boven aan de pagina.
   >
   > U de dubbele informatie bewerken om tags toe te voegen, zoals locatie, en deze in te stellen op een specifieke waarde. Zie [Apparaattweelingen begrijpen en gebruiken in IoT Hub](iot-hub-devguide-device-twins.md)voor meer informatie.

3. Wanneer u klaar bent, moet uw deelvenster er hetzelfde uitzien als deze afbeelding:

   ![Tabel met alle verrijkingen toegevoegd](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Selecteer **Toepassen** om de wijzigingen op te slaan. Ga naar de sectie [Verrijkingen van het testbericht.](#test-message-enrichments)

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Een sjabloon Resourcebeheer maken en configureren met behulp van een sjabloon Resourcebeheer
U een resourcemanagersjabloon gebruiken om de bronnen, berichtroutering en berichtverrijkingen te maken en te configureren.

1. Meld u aan bij Azure Portal. Selecteer **+ Een resource maken** om een zoekvak weer te geven. Voer *sjabloonimplementatie*in en zoek ernaar. Selecteer in het resultatenvenster **sjabloonimplementatie (implementeren met aangepaste sjabloon)**.

   ![Sjabloonimplementatie in de Azure-portal](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Selecteer **Maken** in het **implementatiedeelvenster van sjabloon.**

1. Selecteer **in** het deelvenster Aangepaste implementatie de optie **Uw eigen sjabloon maken in de editor**.

1. Selecteer **Bestand laden**in het deelvenster **Sjabloon bewerken** . Windows Verkenner wordt weergegeven. Zoek het **bestand template_messageenrichments.json** in het uitgepakte repo-bestand in **/iot-hub/Tutorials/Routing/SimulatedDevice/resources**. 

   ![Sjabloon selecteren uit lokale machine](./media/tutorial-message-enrichments/template-select.png)

1. Selecteer **Openen** om het sjabloonbestand van de lokale machine te laden. Het laadt en verschijnt in het bewerkingsvenster.

   Deze sjabloon is ingesteld om een wereldwijd unieke IoT-hubnaam en -opslagaccountnaam te gebruiken door een willekeurige waarde toe te voegen aan het einde van de standaardnamen, zodat u de sjabloon gebruiken zonder er wijzigingen in aan te brengen.

   Hier volgen de bronnen die zijn gemaakt door de sjabloon te laden. **Verrijkt** betekent dat de bron is voor berichten met verrijkingen. **Origineel** betekent dat de bron is voor berichten die niet zijn verrijkt. Dit zijn dezelfde waarden die worden gebruikt in het Azure CLI-script.

   | Naam | Waarde |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | containernaam | Origineel  |
   | containernaam | Verrijkt  |
   | Naam van IoT-apparaat | Contoso-Test-Apparaat |
   | Naam IoT-hub | ContosoTestHubMsgEn |
   | naam van het opslagaccount | contosostorage |
   | eindpuntnaam 1 | ContosoStorageEndpointOrigineel |
   | eindpuntnaam 2 | ContosoStorageEndpointVerrijkt|
   | route naam 1 | ContosoStorageRouteOrigineel |
   | route naam 2 | ContosoStorageRouteVerrijkt |

1. Selecteer **Opslaan**. Het deelvenster **Aangepaste implementatie** wordt weergegeven en toont alle parameters die door de sjabloon worden gebruikt. Het enige veld dat u moet instellen, is **de groep Resource**. Maak een nieuwe of selecteer er een in de vervolgkeuzelijst.

   Hier is de bovenste helft van het **deelvenster Aangepaste implementatie.** U zien waar u de resourcegroep invult.

   ![Bovenste helft van het deelvenster Aangepaste implementatie](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Hier is de onderste helft van het **deelvenster Aangepaste implementatie.** U de rest van de parameters en de algemene voorwaarden zien. 

   ![Onderste helft van het deelvenster Aangepaste implementatie](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Schakel het selectievakje in om akkoord te gaan met de algemene voorwaarden. Selecteer vervolgens **Kopen** om door te gaan met de implementatie van de sjabloon.

1. Wacht tot de sjabloon volledig is geïmplementeerd. Selecteer het belpictogram boven aan het scherm om de voortgang te controleren. Als het klaar is, gaat u verder met de sectie [Verrijkingen van het testbericht.](#test-message-enrichments)

## <a name="test-message-enrichments"></a>Verrijkingen van testberichten

Als u de verrijkingen van het bericht wilt weergeven, selecteert u **Resourcegroepen**. Selecteer vervolgens de resourcegroep die u voor deze zelfstudie gebruikt. Selecteer de IoT-hub in de lijst met bronnen en ga naar **Berichten**. De configuratie van de berichtroutering en de geconfigureerde verrijkingen worden weergegeven.

Nu de doelverrijkingen zijn geconfigureerd voor het eindpunt, voert u de toepassing Gesimuleerd apparaat uit om berichten naar de IoT-hub te verzenden. De hub is ingesteld met instellingen die de volgende taken uitvoeren:

* Berichten die naar het opslageindpunt ContosoStorageEndpointOriginal worden doorgestuurd, worden niet verrijkt `original`en worden opgeslagen in de opslagcontainer.

* Berichten die naar het opslageindpunt ContosoStorageEndpointEnriched worden doorgestuurd, worden `enriched`verrijkt en opgeslagen in de opslagcontainer.

De toepassing Gesimuleerd apparaat is een van de toepassingen in de uitgepakte download. De toepassing verzendt berichten voor elk van de verschillende routeringsmethoden voor berichten in de [zelfstudie Routering,](tutorial-routing.md)waaronder Azure Storage.

Dubbelklik op het oplossingsbestand **IoT_SimulatedDevice.sln** om de code in Visual Studio te openen en open **Program.cs**. Vervang de naam van de `{your hub name}`IoT-hub voor de markering . De indeling van de naam van de IoT-hubhost is **{uw hubnaam}.azure-devices.net**. Voor deze zelfstudie wordt de naam van de hubhost ContosoTestHubMsgEn.azure-devices.net. Vervang vervolgens de apparaatsleutel die u eerder hebt opgeslagen toen u `{your device key}`het script hebt uitgevoerd om de bronnen voor de markering te maken.

Als u de apparaatsleutel niet hebt, u deze ophalen uit de portal. Nadat u zich hebt aangemeld, gaat u naar **Resourcegroepen,** selecteert u uw resourcegroep en selecteert u vervolgens uw IoT-hub. Zoek onder **IoT-apparaten** voor uw testapparaat en selecteer uw apparaat. Selecteer het kopieerpictogram naast **de primaire sleutel** om het naar het klembord te kopiëren.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Uitvoeren en testen

Voer de consoletoepassing een paar minuten uit. De berichten die worden verzonden, worden weergegeven op het consolescherm van de toepassing.

Deze app verzendt elke seconde een nieuw apparaat-naar-cloud-bericht naar de IoT Hub. Het bericht bevat een JSON-geserialiseerd object met een apparaat-id, temperatuur, vochtigheid en berichtniveau, die als standaardwaarde `normal` heeft. Het wijst willekeurig een `critical` niveau `storage`van of , waardoor het bericht wordt doorgestuurd naar de opslag account of naar het standaard eindpunt. De berichten die naar de **verrijkte** container in de opslagrekening worden verzonden, worden verrijkt.

Nadat verschillende opslagberichten zijn verzonden, bekijkt u de gegevens.

1. Selecteer **Resourcegroepen**. Zoek uw **resourcegroep, ContosoResourcesMsgEn,** en selecteer deze.

2. Selecteer uw opslagaccount, dat **is contosostorage**. Selecteer vervolgens **Storage Explorer (voorbeeld)** in het linkerdeelvenster.

   ![Selecteer Storage Explorer](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Selecteer **BLOB-CONTAINERS** om de twee containers te bekijken die kunnen worden gebruikt.

   ![Bekijk de containers in het opslagaccount](./media/tutorial-message-enrichments/show-blob-containers.png)

De berichten in de container genaamd **verrijkt** hebben de boodschap verrijkingen opgenomen in de berichten. De berichten in de container genaamd **origineel** hebben de ruwe berichten zonder verrijkingen. Inzoomen op een van de containers totdat u onderaan bent en open het meest recente berichtenbestand. Doe vervolgens hetzelfde voor de andere container om te controleren of er geen verrijkingen zijn toegevoegd aan berichten in die container.

Als je kijkt naar berichten die zijn verrijkt, moet je "mijn IoT Hub" zien met de hubnaam en de locatie en de klant-ID, zoals deze:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Hier is een niet verrijktbericht. Merk op dat 'mijn IoT Hub', 'devicelocation' en 'customerID' hier niet worden weergegeven omdat deze velden worden toegevoegd door de verrijkingen. Dit eindpunt heeft geen verrijkingen.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle resources wilt verwijderen die u in deze zelfstudie hebt gemaakt, verwijdert u de brongroep. Hiermee verwijdert u ook alle resources in de groep. In dit geval worden de IoT-hub, het opslagaccount en de resourcegroep zelf verwijderd.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>De Azure CLI gebruiken om resources op te schonen

U kunt de resourcegroep verwijderen met de opdracht [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). Recall `$resourceGroup` dat was ingesteld op **ContosoResourcesMsgEn** aan het begin van deze tutorial.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de volgende stappen geconfigureerd en getest bij het toevoegen van berichtverrijkingen aan IoT Hub-berichten:

**Verrijkingen van IoT Hub-berichten gebruiken**
> [!div class="checklist"]
> * Eerste methode: Maak resources en configureer berichtroutering met behulp van de Azure CLI. Configureer de berichtverrijkingen handmatig met behulp van de [Azure-portal.](https://portal.azure.com)
> * Tweede methode: Maak resources en configureer berichtrouteringen en berichtverrijkingen met behulp van een Azure Resource Manager-sjabloon.
> * Voer een app uit die een IoT-apparaat simuleert en berichten naar de hub verzendt.
> * Bekijk de resultaten en controleer of de verrijkingen van berichten werken zoals verwacht.

Zie [Overzicht van berichtverrijkingen](iot-hub-message-enrichments-overview.md)voor meer informatie over berichtverrijkingen.

Zie de volgende artikelen voor meer informatie over het routeren van berichten:

* [IoT Hub-berichtroutering gebruiken om device-to-cloudberichten naar verschillende eindpunten te verzenden](iot-hub-devguide-messages-d2c.md)
* [Zelfstudie: Routering van IoT-hub](tutorial-routing.md)

---
title: Metrische gegevens en diagnostische logboeken instellen en gebruiken met een Azure IoT-hub
description: Meer informatie over het instellen en gebruiken van statistieken en diagnostische logboeken met een Azure IoT-hub. Dit zal gegevens te analyseren om te helpen bij de diagnose van problemen die uw hub kan worden opgelopen.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 3/13/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: d287cfab7adb676d3561486ed34c1062895a4036
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80133638"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>Zelfstudie: statistieken en diagnostische logboeken instellen en gebruiken met een IoT-hub

Als u een IoT Hub-oplossing in een productieomgeving gebruikt, wilt u metrische gegevens instellen en diagnostische logboeken inschakelen. Als er dan een probleem optreedt, hebt u gegevens die u kunt bekijken om het probleem te diagnosticeren en sneller op te lossen. In dit artikel ziet u hoe u de diagnostische logboeken met diagnostische gegevens kunt inschakelen en op fouten controleren. U gaat ook wat metrische gegevens instellen om in de gaten te houden, en waarschuwingen die worden geactiveerd wanneer de metrische gegevens een bepaalde grens raken. U kunt bijvoorbeeld een e-mail naar u laten verzenden wanneer het aantal verzonden telemetrieberichten een bepaalde grens overschrijdt, of wanneer het aantal gebruikte berichten de hoeveelheid toegestane berichten per dag voor de IoT-hub nadert. 

Een voorbeeld van een use-case is een tankstation waar de pompen IoT-apparaten zijn die communiceren met een IoT-hub. Creditcards worden gevalideerd en de uiteindelijke transactie wordt naar een gegevensopslag geschreven. Als de IoT-apparaten stoppen met verbinding maken met de hub en het verzenden van berichten, is het veel moeilijker om dit op te lossen als u geen zicht hebt op wat er aan de hand is.

In deze zelfstudie wordt gebruikgemaakt van het Azure-voorbeeld van de [IoT Hub-routering](tutorial-routing.md) om berichten te verzenden naar de IoT-hub.

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Azure CLI gebruiken om een IoT-hub, een gesimuleerd apparaat en een opslagaccount te maken.  
> * Diagnostische logboeken inschakelen.
> * Metrische gegevens inschakelen.
> * Waarschuwingen voor de metrische gegevens instellen. 
> * Een app downloaden en uitvoeren die een IoT-apparaat simuleert dat berichten naar de hub verzendt. 
> * De app uitvoeren totdat de waarschuwingen worden geactiveerd. 
> * De resultaten van de metrische gegevens bekijken en de logboeken met diagnostische gegevens controleren. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

- Installeer [Visual Studio](https://www.visualstudio.com/). 

- Een e-mailaccount dat e-mail kan ontvangen.

- Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in deze zelfstudie maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Resources instellen

Voor deze zelfstudie hebt u een IoT-hub, een opslagaccount en een gesimuleerd IoT-apparaat nodig. Deze resources kunnen worden gemaakt met Azure CLI of Azure PowerShell. Gebruik dezelfde resourcegroep en -locatie voor alle resources. Vervolgens kunt u alles aan het einde in één stap verwijderen door de resourcegroep te verwijderen.

Dit zijn de vereiste stappen.

1. Een [resourcegroep maken](../azure-resource-manager/management/overview.md). 

2. Maak een IoT-hub.

3. Maak een standaard V1-opslagaccount met Standard_LRS replicatie.

4. Maak een apparaat-id voor het gesimuleerde apparaat dat berichten naar uw hub verzendt. Sla de sleutel op voor de testfase.

### <a name="set-up-resources-using-azure-cli"></a>Resources instellen met behulp van Azure CLI

Kopieer en plak dit script in Cloud Shell. Ervan uitgaande dat u al bent aangemeld, wordt het script met één regel tegelijk uitgevoerd. De nieuwe resources worden gemaakt in de resourcegroep ContosoResources.

Aan de variabelen die globaal uniek moeten zijn, wordt `$RANDOM` toegevoegd. Wanneer het script wordt uitgevoerd en de variabelen worden ingesteld, wordt een willekeurige numerieke reeks gegenereerd en samengevoegd aan het einde van de vaste reeks om deze uniek te maken.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-iot

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku F1 --location $location

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostoragemon$RANDOM
echo "Storage account name = " $storageAccountName

# Create the storage account.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName 

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>Bij het maken van de apparaatidentiteit u de volgende fout krijgen: *er zijn geen sleutels gevonden voor beleidsiothub-eigenaar van IoT Hub ContosoTestHub.* U kunt deze fout corrigeren door de Azure CLI IoT-extensie bij te werken en vervolgens de laatste twee opdrachten in het script opnieuw uit te voeren. 
>
>Hier volgt de opdracht voor het bijwerken van de extensie. Voer deze uit in uw Cloud Shell-instantie.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="enable-the-diagnostic-logs"></a>De diagnostische logboeken inschakelen 

[Diagnostische logboeken](../azure-monitor/platform/platform-logs-overview.md) zijn standaard uitgeschakeld wanneer u een nieuwe IoT-hub maakt. Schakel in deze sectie de diagnostische logboeken voor uw hub in.

1. Als u nog niet op uw hub in de portal bent, klikt u eerst op **Resourcegroepen** en vervolgens op de resourcegroep Contoso-Resources. Selecteer de hub in de lijst met weergegeven resources. 

2. Zoek de sectie **Bewaking** in de IoT Hub-blade. Klik op **Diagnostische instellingen**. 

   ![Schermafbeelding van het gedeelte Diagnostische instellingen van de IoT Hub-blade.](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. Zorg ervoor dat het abonnement en de resourcegroep correct zijn. Schakel onder **Resourcetype** het selectievakje **Alles selecteren** uit, zoek **IoT Hub** op en schakel het in. (Hiermee wordt het vinkje naast Alles opnieuw *selecteren,* negeer het gewoon.) Selecteer **onder Resource**de naam van de hub. Uw scherm moet lijken op deze afbeelding: 

   ![Schermafbeelding van het gedeelte Diagnostische instellingen van de IoT Hub-blade.](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. Klik nu op **Diagnostische gegevens inschakelen**. Het deelvenster Diagnostische instellingen wordt weergegeven. Geef 'diags-hub' op als naam van uw instellingen voor diagnostische logboeken.

5. Schakel **Archiveren naar een opslagaccount** in. 

   ![Schermafbeelding van het instellen van de diagnostische gegevens om te worden gearchiveerd naar een opslagaccount.](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    Klik op **Configureren** om naar het scherm **Een opslagaccount selecteren** te gaan, selecteer het juiste account (*contosostoragemon*) en klik op **OK** om terug te gaan naar het deelvenster Diagnostische instellingen. 

   ![Schermafbeelding van het instellen van de diagnostische-gegevenslogboeken om te worden gearchiveerd naar een opslagaccount.](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. Schakel onder **LOG** de vakjes **Verbindingen** en **Apparaattelemetrie** in, en stel **Retentie (dagen)** bij beide in op 7 dagen. Uw Diagnostische instellingen-scherm zou er nu uit moeten zien als in deze afbeelding:

   ![Schermafbeelding met de uiteindelijke diagnostische-logboekinstellingen.](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. Klik op **Opslaan** om de instellingen op te slaan. Sluit het deelvenster Diagnostische instellingen.

Wanneer u later de diagnostische logboeken bekijkt, kunt u de vermeldingen over het tot stand brengen en verbreken van de verbinding met het apparaat zien. 

## <a name="set-up-metrics"></a>Metrische gegevens instellen 

Stel nu wat metrische gegevens in om naar te kijken wanneer berichten naar hub worden verzonden. 

1. Klik in het deelvenster met instellingen voor de IoT-hub op de optie **Metrische gegevens** in de sectie **Bewaking**.

2. Klik bovenaan het scherm op **Afgelopen 24 uur (automatisch)**. Selecteer in de vervolgkeuzelijst die wordt weergegeven **Afgelopen 4 uur** in bij **Tijdsbereik**, en stel **Tijdgranulatie** in op **1 minuut**, lokale tijd. Klik op **Toepassen** om deze instellingen op te slaan. 

   ![Schermafbeelding van de tijdinstellingen voor de metrische gegevens.](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. Er is standaard één metrische vermelding. Laat de resourcegroep staan op de standaardinstelling, en de metrische naamruimte. Selecteer in de vervolgkeuzelijst **Metrische waarde** de optie **Verzonden telemetrieberichten**. Stel **Aggregatie** in op **Som**.

   ![Schermafbeelding van het toevoegen van een metrische waarde voor verzonden telemetrieberichten.](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. Klik nu op **Metrische waarde toevoegen** om nog een metrische waarde toe te voegen aan de grafiek. Selecteer uw resourcegroep (**ContosoTestHub**). Selecteer onder **Metrische waarde**, selecteer **Totaal aantal gebruikte berichten**. Selecteer **Gem** bij **Aggregatie**. 

   Nu wordt op uw scherm de geminimaliseerde waarde weergegeven voor *Verzonden telemetrieberichten*, plus de nieuwe metrische waarde voor *Totaal aantal gebruikte berichten*.

   ![Schermafbeelding van het toevoegen van een metrische waarde voor verzonden telemetrieberichten.](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   Klik op **Vastmaken aan dashboard**. Hierdoor wordt de waarde vastgemaakt aan het dashboard van uw Azure-portal, zodat u er opnieuw toegang tot hebt. Als u de waarde niet vastmaakt aan het dashboard, blijven uw instellingen niet behouden.

## <a name="set-up-alerts"></a>Waarschuwingen instellen

Ga naar de hub in de portal. Klik op **Resourcegroepen** en selecteer *ContosoResources* en vervolgens IoT Hub *ContosoTestHub*. 

IoT Hub is nog niet gemigreerd naar de [metrische gegevens in Azure Monitor](/azure/azure-monitor/platform/data-collection#metrics); u moet [klassieke waarschuwingen](/azure/azure-monitor/platform/alerts-classic.overview) gebruiken.

1. Klik onder **Bewaking** op **Waarschuwingen**. Hierdoor wordt het hoofdscherm met waarschuwingen weergegeven. 

   ![Schermafbeelding die laat zien hoe klassieke waarschuwingen te vinden zijn.](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. Klik van hieruit op **Klassieke waarschuwingen weergeven** om naar de klassieke waarschuwingen te gaan. 

    ![Schermafbeelding van scherm voor klassieke waarschuwingen.](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    Vul de velden in: 

    **Abonnement:** Laat dit veld instellen op uw huidige abonnement.

    **Bron:** Stel dit veld in *op Statistieken*.

    **Resourcegroep:** Stel dit veld in op uw huidige *resourcegroep, ContosoResources*. 

    **Resourcetype:** Stel dit veld in op IoT Hub. 

    **Resource:** Selecteer uw IoT-hub, *ContosoTestHub*.

3. Klik op **Waarschuwing voor metrische gegevens toevoegen (klassiek)** om een nieuwe waarschuwing in te stellen.

    Vul de velden in:

    **Naam:** Geef een naam op voor uw waarschuwingsregel, zoals *telemetrieberichten.*

    **Beschrijving**: Geef een beschrijving van uw waarschuwing, zoals *waarschuwing wanneer er 1000 telemetrieberichten worden verzonden.* 

    **Bron:** Stel dit in *op Statistieken*.

    **Abonnement**, **Resourcegroep** en **Resource** moeten worden ingesteld op de waarden die u hebt geselecteerd in het scherm **Klassieke waarschuwingen weergeven**. 

    Stel **Metrische gegevens** in op *Verzonden telemetrieberichten*.

    ![Schermafbeelding van het instellen van een klassieke waarschuwing voor verzonden telemetrieberichten.](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. Stel na de grafiek de volgende velden in:

   **Voorwaarde**: Ingesteld op *Groter dan*.

   **Drempel:** Ingesteld op 1000.

   **Periode**: Ingesteld *op Over de laatste 5 minuten*.

   **Ontvangers van e-mailmeldingen:** Plaats hier uw e-mailadres. 

   ![Schermafbeelding van de onderste helft van het waarschuwingenscherm.](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   Klik op **OK** om de waarschuwing op te slaan. 

5. Stel nu een andere waarschuwing in voor het *totale aantal gebruikte berichten*. Deze waarde is handig als u een waarschuwing wilt verzenden wanneer het aantal gebruikte berichten het quotum voor de IoT-hub nadert, om u te laten weten dat de hub binnenkort berichten zal gaan weigeren.

   Klik in het scherm **Klassieke waarschuwingen weergeven** op **Waarschuwing voor metrische gegevens toevoegen (klassiek)** en vul vervolgens in het deelvenster **Regel toevoegen** deze velden in.

   **Naam:** Geef een naam op voor uw waarschuwingsregel, zoals het aantal gebruikte *berichten.*

   **Beschrijving**: Geef een beschrijving van uw waarschuwing, zoals *waarschuwing bij het in de buurt komen van het quotum.*

   **Bron:** Stel dit veld in *op Statistieken*.

    **Abonnement**, **Resourcegroep** en **Resource** moeten worden ingesteld op de waarden die u hebt geselecteerd in het scherm **Klassieke waarschuwingen weergeven**. 

    Stel **Metrische waarde** in op *Totaal aantal gebruikte berichten*.

6. Vul onder de grafiek de volgende velden in:

   **Voorwaarde**: Ingesteld op *Groter dan*.

   **Drempel:** Ingesteld op 1000.

   **Periode**: Stel dit veld in *op Over de laatste 5 minuten*. 

   **Ontvangers van e-mailmeldingen:** Plaats hier uw e-mailadres. 

   Klik op **OK** om de regel op te slaan. 

5. U ziet nu twee waarschuwingen in het deelvenster voor klassieke waarschuwingen: 

   ![Schermafbeelding van het scherm voor klassieke waarschuwingen met de nieuwe waarschuwingsregels.](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. Sluit het waarschuwingenvenster. 
    
    Met deze instellingen krijgt u een waarschuwing wanneer het aantal verzonden berichten groter is dan 400 en wanneer het totale aantal gebruikte berichten groter is dan AANTAL.

## <a name="run-simulated-device-app"></a>De app Gesimuleerd apparaat uitvoeren

Eerder in het scriptinstellingsgedeelte stelt u een apparaat in voor de simulatie via een IoT-apparaat. In deze sectie downloadt u een .NET-consoletoepassing die een apparaat simuleert dat apparaat-naar-cloud-berichten naar een IoT Hub verzendt.  

Download de oplossing voor de [IoT-apparaatsimulatie](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Deze link downloadt een repo met verschillende toepassingen in; de oplossing die u zoekt is in iot-hub/Tutorials/Routing/.

Dubbelklik op het oplossingsbestand (SimulatedDevice.sln om de code in Visual Studio) te openen en open vervolgens Program.cs. Vervang `{iot hub hostname}` door de hostnaam van de IoT Hub. De indeling van de hostnaam van de IoT Hub is **{iot-hub-name} .azure-devices.net**. Voor deze zelfstudie is de naam van de hubhost **ContosoTestHub.azure devices.net**. Vervang vervolgens `{device key}` door de apparaatsleutel die u eerder hebt opgeslagen bij het instellen van het gesimuleerde apparaat. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Uitvoeren en testen 

Wijzig in Program.cs de `Task.Delay` van 1000 in 10, waardoor de tijd tussen het verzenden van berichten verandert van 1 seconde in 0,01 seconden. Door deze vertraging te verkorten, wordt het aantal verzonden berichten vergroot.

```csharp
await Task.Delay(10);
```

Voer de consoletoepassing uit. Wacht enkele minuten (10–15). U kunt de berichten die vanaf het gesimuleerde apparaat naar de hub worden verzonden, zien op het consolescherm van de toepassing.

### <a name="see-the-metrics-in-the-portal"></a>De metrische gegevens bekijken in de portal

Open uw metrische gegevens vanuit het Dashboard. Wijzig de tijdwaarden in *Afgelopen 30 minuten*, met een tijdgranulatie van *1 minuut*. De verzonden telemetrieberichten en het totale aantal gebruikte berichten worden weergegeven in de grafiek, met de meest recente cijfers onderaan de grafiek.

   ![Schermafbeelding van de metrische gegevens.](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>De waarschuwingen bekijken

Ga terug naar waarschuwingen. Klik op **Resourcegroepen** en selecteer *ContosoResources* en vervolgens de hub *ContosoTestHub*. Selecteer **Waarschuwingen** op de eigenschappenpagina voor de hub die wordt weergegeven en vervolgens **Klassieke waarschuwingen weergeven**. 

Wanneer het aantal verzonden berichten de limiet overschrijdt, begint u e-mailwaarschuwingen te krijgen. Als u wilt zien of er actieve waarschuwingen zijn, gaat u naar uw hub en selecteert u **Waarschuwingen**. Hier ziet u de meldingen die actief zijn, en of er waarschuwingen zijn. 

   ![Schermafbeelding die laat zien dat de waarschuwingen zijn geactiveerd.](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

Klik op de waarschuwing voor telemetrieberichten. Het resultaat van de metrische gegevens en een grafiek met de resultaten worden weergegeven. Het e-mailbericht dat wordt verzonden om u te waarschuwen ziet eruit als in deze afbeelding:

   ![Schermafbeelding van het e-mailbericht dat laat zien dat de waarschuwingen zijn geactiveerd.](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>De diagnostische logboeken bekijken

U hebt uw diagnostische logboeken zo ingesteld dat ze naar blob-opslag worden geëxporteerd. Ga naar de resourcegroep en selecteer uw opslagaccount *contosostoragemon*. Selecteer Blobs en open de container *insights-logs-connections*. Zoom in tot u bij de huidige datum bent en selecteer het meest recente bestand. 

   ![Schermafbeelding van het inzoomen op de opslagcontainer voor de diagnostische logboeken.](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

Klik op **Downloaden** om het downloaden en te openen. U ziet de logboekitems van het apparaat dat verbinding maakt en verbreekt bij het verzenden van berichten naar de hub. Hier ziet u een voorbeeld:

``` json
{ 
  "time": "2018-12-17T18:11:25Z", 
  "resourceId": 
    "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
  "operationName": "deviceConnect", 
  "category": "Connections", 
  "level": "Information", 
  "properties": 
      {"deviceId":"Contoso-Test-Device",
       "protocol":"Mqtt",
       "authType":null,
       "maskedIpAddress":"73.162.215.XXX",
       "statusCode":null
       }, 
  "location": "westus"
}
{ 
   "time": "2018-12-17T18:19:25Z", 
   "resourceId": 
     "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
    "operationName": "deviceDisconnect", 
    "category": "Connections", 
    "level": "Error", 
    "resultType": "404104", 
    "resultDescription": "DeviceConnectionClosedRemotely", 
    "properties": 
        {"deviceId":"Contoso-Test-Device",
         "protocol":"Mqtt",
         "authType":null,
         "maskedIpAddress":"73.162.215.XXX",
         "statusCode":"404"
         }, 
    "location": "westus"
}
```

## <a name="clean-up-resources"></a>Resources opschonen 

Als u alle resources die u in deze zelfstudie hebt gemaakt, wilt verwijderen, verwijdert u de resourcegroep. Hiermee verwijdert u ook alle resources in de groep. In dit geval worden de IoT-hub, het opslagaccount en de resourcegroep zelf verwijderd. Als u metrische gegevens aan het dashboard hebt vastgemaakt, moet u die handmatig verwijderen door op de drie puntjes in de rechterbovenhoek van elk ervan te klikken en **Verwijderen** te selecteren.

U kunt de resourcegroep verwijderen met de opdracht [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd metrische gegevens en logboeken met diagnostische gegevens te gebruiken door de volgende taken uitvoeren:

> [!div class="checklist"]
> * Azure CLI gebruiken om een IoT-hub, een gesimuleerd apparaat en een opslagaccount te maken.  
> * Diagnostische logboeken inschakelen. 
> * Metrische gegevens inschakelen.
> * Waarschuwingen voor de metrische gegevens instellen. 
> * Een app downloaden en uitvoeren die een IoT-apparaat simuleert dat berichten naar de hub verzendt. 
> * De app uitvoeren totdat de waarschuwingen worden geactiveerd. 
> * De resultaten van de metrische gegevens bekijken en de logboeken met diagnostische gegevens controleren. 

Ga door naar de volgende zelfstudie voor informatie over het beheren van de toestand van een IoT-apparaat. 

> [!div class="nextstepaction"]
> [Uw apparaten configureren vanaf een back-endservice](tutorial-device-twins.md)

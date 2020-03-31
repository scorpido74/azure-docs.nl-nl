---
title: Verbindingsgebeurtenissen voor apparaten ordenen fr Azure IoT Hub w/Azure Cosmos DB
description: In dit artikel wordt beschreven hoe u apparaatverbindingsgebeurtenissen van Azure IoT Hub ordenen en opnemen met Azure Cosmos DB om de nieuwste verbindingsstatus te behouden
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.openlocfilehash: 210c2e74305ba99b4ac3a12625d0b7f5fc47ba43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954256"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Verbindingsgebeurtenissen voor het apparaat aanvragen bij Azure IoT Hub met behulp van Azure Cosmos DB

Azure Event Grid helpt u bij het bouwen van op evenementen gebaseerde toepassingen en het eenvoudig integreren van IoT-gebeurtenissen in uw bedrijfsoplossingen. Dit artikel leidt u door een opstelling die kan worden gebruikt om de recentste status van de apparaatverbinding in Kosmos DB te volgen en op te slaan. We gebruiken het volgnummer dat beschikbaar is in de gebeurtenissen Device Connected en Device Disconnected en slaan de nieuwste status op in Cosmos DB. We gaan een opgeslagen procedure gebruiken, een toepassingslogica die wordt uitgevoerd tegen een verzameling in Cosmos DB.

Het volgnummer is een tekenreeksweergave van een hexadecimaal getal. U tekenreeksvergelijken gebruiken om het grotere getal te identificeren. Als u de tekenreeks omzet in hex, is het getal een 256-bits getal. Het volgnummer neemt strikt toe en de laatste gebeurtenis heeft een hoger aantal dan andere gebeurtenissen. Dit is handig als u regelmatig verbinding maakt met het apparaat en de verbinding verbreekt en ervoor wilt zorgen dat alleen de nieuwste gebeurtenis wordt gebruikt om een downstream-actie te activeren, omdat Azure Event Grid geen ondersteuning biedt voor het bestellen van gebeurtenissen.

## <a name="prerequisites"></a>Vereisten

* Een actief Azure-account. Als je nog geen account hebt, kun je [een gratis account aanmaken.](https://azure.microsoft.com/pricing/free-trial/)

* Een actief Azure Cosmos DB SQL API-account. Zie [Een databaseaccount maken](../cosmos-db/create-sql-api-java.md#create-a-database-account) voor een walkthrough als u er nog geen hebt gemaakt.

* Een verzameling in uw database. Zie [Een verzameling toevoegen](../cosmos-db/create-sql-api-java.md#add-a-container) voor een walkthrough. Wanneer u uw verzameling `/id` maakt, gebruikt u de partitiesleutel.

* Een IoT Hub in Azure. Als u nog geen hub hebt gemaakt, leest u [Get started with IoT Hub](iot-hub-csharp-csharp-getstarted.md) (Aan de slag met IoT Hub) voor stapsgewijze instructies.

## <a name="create-a-stored-procedure"></a>Een opgeslagen procedure maken

Maak eerst een opgeslagen procedure en stel deze in om een logica uit te voeren die volgnummers van binnenkomende gebeurtenissen vergelijkt en de laatste gebeurtenis per apparaat in de database registreert.

1. Selecteer in de Cosmos DB SQL API de optie **Gegevensverkenner-items** > **Items** > **Nieuwe opgeslagen procedure**.

   ![Opgeslagen procedure maken](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Voer **LatestDeviceConnectionState** in voor de opgeslagen procedure-id en plak het volgende in de **hoofdtekst Opgeslagen procedure**. Houd er rekening mee dat deze code elke bestaande code in de opgeslagen proceduretekst moet vervangen. Deze code houdt één rij per apparaat-id bij en registreert de nieuwste verbindingsstatus van die apparaat-id door het hoogste volgnummer te identificeren.

    ```javascript
    // SAMPLE STORED PROCEDURE
    function UpdateDevice(deviceId, moduleId, hubName, connectionState, connectionStateUpdatedTime, sequenceNumber) {
      var collection = getContext().getCollection();
      var response = {};

      var docLink = getDocumentLink(deviceId, moduleId);

      var isAccepted = collection.readDocument(docLink, function(err, doc) {
        if (err) {
          console.log('Cannot find device ' + docLink + ' - ');
          createDocument();
        } else {
          console.log('Document Found - ');
          replaceDocument(doc);
        }
      });

      function replaceDocument(document) {
        console.log(
          'Old Seq :' +
            document.sequenceNumber +
            ' New Seq: ' +
            sequenceNumber +
            ' - '
        );
        if (sequenceNumber > document.sequenceNumber) {
          document.connectionState = connectionState;
          document.connectionStateUpdatedTime = connectionStateUpdatedTime;
          document.sequenceNumber = sequenceNumber;

          console.log('replace doc - ');

          isAccepted = collection.replaceDocument(docLink, document, function(
            err,
            updated
          ) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(updated);
            }
          });
        } else {
          getContext()
            .getResponse()
            .setBody('Old Event - current: ' + document.sequenceNumber + ' Incoming: ' + sequenceNumber);
        }
      }
      function createDocument() {
        document = {
          id: deviceId + '-' + moduleId,
          deviceId: deviceId,
          moduleId: moduleId,
          hubName: hubName,
          connectionState: connectionState,
          connectionStateUpdatedTime: connectionStateUpdatedTime,
          sequenceNumber: sequenceNumber
        };
        console.log('Add new device - ' + collection.getAltLink());
        isAccepted = collection.createDocument(
          collection.getAltLink(),
          document,
          function(err, doc) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(doc);
            }
          }
        );
      }

      function getDocumentLink(deviceId, moduleId) {
        return collection.getAltLink() + '/docs/' + deviceId + '-' + moduleId;
      }
    }
    ```

3. Sla de opgeslagen procedure op:

    ![opgeslagen procedure opslaan](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Een logische app maken

U gaat eerst een logische app maken en een trigger voor Event Grid toevoegen die de resourcegroep voor uw virtuele machine bewaakt.

### <a name="create-a-logic-app-resource"></a>Een logische app maken

1. Selecteer in de [Azure-portal](https://portal.azure.com) **+Een resource maken,** selecteer **Integratie** en vervolgens **Logic App**.

   ![Logische app maken](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Geef een naam op voor de logische app die uniek is in uw abonnement en selecteer vervolgens het abonnement, de resourcegroep en de locatie van uw IoT-hub.

   ![Nieuwe logische app](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Selecteer **Maken** om de logische app te maken.

   U hebt nu een Azure-resource voor uw logische app gemaakt. Nadat de logische app is geïmplementeerd, toont de ontwerper van logische apps sjablonen voor algemene patronen, zodat u sneller aan de slag kunt.

   > [!NOTE]
   > Als u uw logische app opnieuw wilt zoeken en openen, selecteert u **Resourcegroepen** en selecteert u de resourcegroep die u voor deze how-to gebruikt. Selecteer vervolgens uw nieuwe logica-app. Hiermee wordt de Logic App Designer geopend.

4. Schuif in de Logic App Designer naar rechts totdat u veelvoorkomende triggers ziet. Kies **onder Sjablonen**de optie Blank Logic **App,** zodat u uw logische app helemaal opnieuw bouwen.

### <a name="select-a-trigger"></a>Een trigger selecteren

Een trigger is een specifieke gebeurtenis waarmee uw logische app wordt gestart. Voor deze zelfstudie is de trigger voor het activeren van de werkstroom het ontvangen van een aanvraag via HTTP.

1. Typ **HTTP** en druk op Enter in de zoekbalk en activeert deze.

2. Selecteer **Aanvraag - Wanneer een HTTP-aanvraag is ontvangen** als de trigger.

   ![Selecteer de trigger Aanvraag - Wanneer een HTTP-aanvraag is ontvangen](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Selecteer **Voorbeeldnettolading om een schema te genereren**.

   ![Voorbeeldpayload gebruiken om een schema te genereren](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

4. Plak de volgende voorbeeldcode van JSON in het tekstvak en selecteer vervolgens **Gereed**:

   ```json
   [{
    "id": "fbfd8ee1-cf78-74c6-dbcf-e1c58638ccbd",
    "topic":
      "/SUBSCRIPTIONS/DEMO5CDD-8DAB-4CF4-9B2F-C22E8A755472/RESOURCEGROUPS/EGTESTRG/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/MYIOTHUB",
    "subject": "devices/Demo-Device-1",
    "eventType": "Microsoft.Devices.DeviceConnected",
    "eventTime": "2018-07-03T23:20:11.6921933+00:00",
    "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
      "hubName": "MYIOTHUB",
      "deviceId": "48e44e11-1437-4907-83b1-4a8d7e89859e",
      "moduleId": ""
    },
    "dataVersion": "1",
    "metadataVersion": "1"
   }]
   ```

   ![Het monster JSON-payload plakken](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Er kan een melding worden weergegeven dat u niet moet vergeten **in uw aanvraag een header Content-type op te nemen die is ingesteld op application/json**. U kunt deze suggestie zonder problemen negeren en verdergaan met de volgende sectie.

### <a name="create-a-condition"></a>Een voorwaarde maken

In uw logische app-werkstroom helpen voorwaarden bij het uitvoeren van specifieke acties nadat u die specifieke voorwaarde hebt doorstaan. Zodra aan de voorwaarde is voldaan, kan een gewenste actie worden gedefinieerd. Voor deze zelfstudie moet u controleren of eventType is aangesloten op het apparaat of of het apparaat is losgekoppeld. De actie is om de opgeslagen procedure in uw database uit te voeren.

1. Selecteer **+ Nieuwe stap** en vervolgens **Ingebouwd**en zoek en selecteer **Voorwaarde**. Klik in **Een waarde kiezen** en er verschijnt een vak met de dynamische inhoud, de velden die kunnen worden geselecteerd. Vul de velden in zoals hieronder wordt weergegeven om dit alleen uit te voeren voor apparaatverbonden en apparaatniet verbonden gebeurtenissen:

   * Kies een waarde: **eventType** - selecteer dit in de velden in de dynamische inhoud die worden weergegeven wanneer u op dit veld klikt.
   * Verandering "is gelijk aan" om te **eindigen met**.
   * Kies een waarde: **nected**.

     ![Vulvoorwaarde](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. Klik **in** het dialoogvenster Als waar op **Een actie toevoegen**.
  
   ![Actie toevoegen als true](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Zoeken naar Cosmos DB en selecteer **Azure Cosmos DB - Opgeslagen procedure uitvoeren**

   ![Zoek naar CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Vul **de cosmosdb-verbinding** voor de **verbindingsnaam** in en selecteer de vermelding in de tabel en selecteer **Vervolgens Maken**. U ziet het deelvenster **Opgeslagen procedure uitvoeren.** Voer de waarden voor de velden in:

   **Database-ID**: ToDoList

   **Collectie-id**: Objecten

   **Sproc ID**: LatestDeviceConnectionState

5. Selecteer **Nieuwe parameter toevoegen**. Schakel in de vervolgkeuzelijst die wordt weergegeven de selectievakjes naast **partitiesleutel** en **Parameters voor de opgeslagen procedure**in en klik vervolgens ergens anders op het scherm. het voegt een veld voor partitiesleutelwaarde en een veld voor parameters voor de opgeslagen procedure toe.

   ![logica-app-actie invullen](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Voer nu de waarde en parameters van de partitiesleutel in, zoals hieronder wordt weergegeven. Zorg ervoor dat je in de beugels en dubbele quotes zoals afgebeeld. Mogelijk moet u op **Dynamische inhoud toevoegen** klikken om de geldige waarden te krijgen die u hier gebruiken.

   ![logica-app-actie invullen](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. Zorg er boven aan het deelvenster waar **het voor elke**staat , onder Een uitvoer uit eerdere stappen **selecteren**, of deze **hoofdtekst** is geselecteerd.

   ![logische app voor elk invullen](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Sla uw logische app op.

### <a name="copy-the-http-url"></a>HTTP-URL kopiëren

Voordat u de Logic Apps Designer verlaat, kopieert u de URL waarnaar uw logische app luistert voor een trigger. U gebruikt deze URL voor het configureren van Event Grid.

1. Vouw het configuratievak **Wanneer een HTTP-aanvraag is ontvangen** uit door erop te klikken.

2. Kopieer de waarde van **URL voor HTTP POST** door de knop URL kopiëren ernaast te selecteren.

   ![De URL voor HTTP POST kopiëren](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Sla deze URL op zodat u ernaar kunt verwijzen in de volgende sectie.

## <a name="configure-subscription-for-iot-hub-events"></a>Abonnement voor IoT Hub-gebeurtenissen configureren

In deze sectie configureert u de IoT-hub voor het publiceren van gebeurtenissen op het moment dat deze optreden.

1. Ga in Azure Portal naar uw IoT-hub.

2. Selecteer **Gebeurtenissen**.

   ![Details van gebeurtenisraster weergeven](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Selecteer **+ Abonnement op evenementen**.

   ![Nieuw gebeurtenisabonnement maken](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Details **van het gebeurtenisabonnement**invullen: geef een beschrijvende naam op en selecteer **gebeurtenisrasterschema**.

5. Vul de velden **Gebeurtenistypen** in. Selecteer in de vervolgkeuzelijst alleen **verbonden apparaat** en **apparaat dat is losgekoppeld** in het menu. Klik ergens anders op het scherm om de lijst te sluiten en uw selecties op te slaan.

   ![Gebeurtenistypen instellen om naar te zoeken](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. Selecteer Voor **Eindpuntdetails**Eindpunttype als **webhaak** en klik op selecteer eindpunt en plak de URL die u hebt gekopieerd uit uw logische app en bevestig selectie.

   ![Eindpunturl selecteren](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. Het formulier moet er nu hetzelfde uitzien als het volgende voorbeeld:

   ![Voorbeeld van formulier voor gebeurtenisabonnement](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Selecteer **Maken** om het gebeurtenisabonnement op te slaan.

## <a name="observe-events"></a>Gebeurtenissen observeren

Nu uw evenementabonnement is ingesteld, laten we testen door een apparaat aan te sluiten.

### <a name="register-a-device-in-iot-hub"></a>Een apparaat registreren in IoT-hub

1. Selecteer **IoT-apparaten** in de IoT-hub.

2. Selecteer **+Toevoegen** boven aan het deelvenster.

3. Geef `Demo-Device-1` op voor **Apparaat-ID**.

4. Selecteer **Opslaan**.

5. U meerdere apparaten met verschillende apparaat-id's toevoegen.

   ![Apparaten toegevoegd aan hub](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Klik nogmaals op het apparaat; nu worden de verbindingstekenreeksen en toetsen ingevuld. Kopieer de **verbindingstekenreeks - primaire sleutel** voor later gebruik.

   ![Verbindingstekenreeks voor apparaat](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Raspberry Pi-simulator starten

Laten we de Raspberry Pi-websimulator gebruiken om de apparaatverbinding te simuleren.

[Raspberry Pi-simulator starten](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Een voorbeeldtoepassing uitvoeren op de Raspberry Pi-websimulator

Dit activeert een apparaat verbonden gebeurtenis.

1. Vervang in het coderingsgebied de tijdelijke aanduiding in lijn 15 door de verbindingstekenreeks van uw Azure IoT Hub-apparaat die u aan het einde van de vorige sectie hebt opgeslagen.

   ![Plak in de verbindingstekenreeks van het apparaat](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Voer de toepassing uit door **Uitvoeren te**selecteren.

U ziet iets vergelijkbaars met de volgende uitvoer die de sensorgegevens en de berichten die naar uw IoT-hub worden verzonden, weergeeft.

   ![De toepassing uitvoeren](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Klik **op Stoppen** om de simulator te stoppen en een gebeurtenis Apparaat losgekoppeld **te** activeren.

U hebt nu een voorbeeldtoepassing uitgevoerd om sensorgegevens te verzamelen en naar uw IoT-hub te verzenden.

### <a name="observe-events-in-cosmos-db"></a>Bekijk gebeurtenissen in Cosmos DB

U de resultaten van de uitgevoerde opgeslagen procedure in uw Cosmos DB-document bekijken. Hier ziet u hoe dat eruit ziet. Elke rij bevat de nieuwste apparaatverbindingsstatus per apparaat.

   ![Hoe resultaat](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

In plaats van de [Azure-portal](https://portal.azure.com)te gebruiken, u de stappen van de IoT-hub uitvoeren met de Azure CLI. Zie de Azure CLI-pagina's voor [het maken van een gebeurtenisabonnement](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) en het maken van een [IoT-apparaat](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create)voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

In deze zelfstudie zijn resources gebruikt die kosten voor uw Azure-abonnement met zich meebrengen. Wanneer u klaar bent met het uitproberen van de zelfstudie en het testen van uw resultaten, schakelt u resources uit of verwijdert die u niet wilt behouden.

Als u het werk aan uw logische app wilt behouden, kunt u de app uitschakelen in plaats van verwijderen.

1. Ga naar uw logische app.

2. Selecteer in het **blad Overzicht** de optie **Verwijderen** of **Uitschakelen**.

    Elk abonnement biedt toegang tot één gratis IoT-hub. Als u een gratis hub hebt gemaakt voor deze zelfstudie, hoeft u deze niet te verwijderen om te voorkomen dat er kosten in rekening worden gebracht.

3. Ga naar uw IoT-hub.

4. Selecteer **verwijderen**in het **blad Overzicht** .

    Zelfs als u uw IoT-hub wilt behouden, kunt u het gebeurtenisabonnement verwijderen dat u hebt gemaakt.

5. Selecteer hiervoor **Event Grid** in uw IoT-hub.

6. Selecteer het gebeurtenisabonnement dat u wilt verwijderen.

7. Selecteer **Verwijderen**.

Als u een Azure Cosmos DB-account wilt verwijderen uit de Azure-portal, klikt u met de rechtermuisknop op de accountnaam en klikt u op **Account verwijderen**. Zie gedetailleerde instructies voor [het verwijderen van een Azure Cosmos DB-account](https://docs.microsoft.com/azure/cosmos-db/manage-account).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [reageren op IoT Hub-gebeurtenissen door gebeurtenisraster te gebruiken om acties te activeren](../iot-hub/iot-hub-event-grid.md)

* [Probeer de zelfstudie gebeurtenissen van iot-hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Meer informatie over wat u nog meer doen met [Event Grid](../event-grid/overview.md)
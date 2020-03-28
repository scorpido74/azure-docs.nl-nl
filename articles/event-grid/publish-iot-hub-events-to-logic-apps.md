---
title: Zelfstudie - IoT Hub-gebeurtenissen gebruiken om Azure Logic Apps te activeren
description: In deze zelfstudie ziet u hoe u de gebeurtenisrouteringsservice van Azure Event Grid gebruiken en geautomatiseerde processen maken om Azure Logic Apps-acties uit te voeren op basis van IoT Hub-gebeurtenissen.
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: robinsh
ms.openlocfilehash: 334b7b2c59b328e8eff3c7c2b9c3ed46bffc3442
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74706439"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Zelfstudie: E-mailmeldingen verzenden over Azure IoT Hub-gebeurtenissen met gebeurtenisraster en logische apps

Azure Event Grid maakt het mogelijk om te reageren op gebeurtenissen in IoT Hub door acties in zakelijke toepassingen verderop in de werkstroom te activeren.

In dit artikel wordt een voorbeeldconfiguratie doorlopen die gebruikmaakt van IoT Hub en Event Grid. Aan het einde hebt u een Azure-logica-app ingesteld om een meldingsmail te verzenden telkens wanneer een apparaat aan uw IoT-hub wordt toegevoegd. 

## <a name="prerequisites"></a>Vereisten

* Een e-mailaccount van een e-mailprovider die door Azure Logic Apps wordt ondersteund, bijvoorbeeld Office 365 Outlook, Outlook.com of Gmail. Dit e-mailaccount wordt gebruikt voor het verzenden van de gebeurtenismeldingen. Zie [Overzicht van connectors](https://docs.microsoft.com/connectors/) voor een volledige lijst met ondersteunde Logic App-connectors.
* Een actief Azure-account. Als je nog geen account hebt, kun je [een gratis account aanmaken.](https://azure.microsoft.com/pricing/free-trial/)
* Een IoT Hub in Azure. Als u nog geen hub hebt gemaakt, leest u [Get started with IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md) (Aan de slag met IoT Hub) voor stapsgewijze instructies. 

## <a name="create-a-logic-app"></a>Een logische app maken

Maak eerst een logische app en voeg een gebeurtenisrastertrigger toe die de brongroep voor uw virtuele machine bewaakt. 

### <a name="create-a-logic-app-resource"></a>Een logische app maken

1. Selecteer in de [Azure-portal](https://portal.azure.com) **Een resource maken**en typ vervolgens 'logische app' in het zoekvak en selecteer return. Selecteer **Logic App** in de resultaten.

   ![Logische app maken](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. Selecteer op het volgende scherm **Maken**. 

1. Geef een naam op voor de logische app die uniek is in uw abonnement en selecteer vervolgens het abonnement, de resourcegroep en de locatie van uw IoT-hub. 

   ![Velden voor logische app maken](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Selecteer **Maken**.

1. Als de resource is gemaakt, navigeert u naar uw logische app. Selecteer hiervoor **Resourcegroepen**en selecteer vervolgens de resourcegroep die u voor deze zelfstudie hebt gemaakt. Zoek vervolgens de logische app in de lijst met bronnen en selecteer deze. 

1. Ga in de Logic Apps Designer naar beneden om sjablonen weer te **geven.** Kies **Blank Logic App,** zodat u uw logica-app helemaal opnieuw bouwen.

### <a name="select-a-trigger"></a>Een trigger selecteren

Een trigger is een specifieke gebeurtenis waarmee uw logische app wordt gestart. Voor deze zelfstudie is de trigger voor het activeren van de werkstroom het ontvangen van een aanvraag via HTTP.  

1. Typ **HTTP** in de zoekbalk voor connectors en triggers.

1. Selecteer **Aanvraag - Wanneer een HTTP-aanvraag is ontvangen** als de trigger. 

   ![Selecteer de trigger Aanvraag - Wanneer een HTTP-aanvraag is ontvangen](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. Selecteer **Voorbeeldnettolading om een schema te genereren**. 

   ![Selecteer de trigger Aanvraag - Wanneer een HTTP-aanvraag is ontvangen](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. Plak de volgende voorbeeldcode van JSON in het tekstvak en selecteer vervolgens **Gereed**:

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "deviceEtag": "null",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice"
     },
     "dataVersion": "1",
     "metadataVersion": "1"
   }]
   ```

1. Er kan een melding worden weergegeven dat u niet moet vergeten **in uw aanvraag een header Content-type op te nemen die is ingesteld op application/json**. U kunt deze suggestie zonder problemen negeren en verdergaan met de volgende sectie. 

### <a name="create-an-action"></a>Een actie maken

Acties zijn stappen die worden uitgevoerd nadat de trigger de werkstroom van de logische app heeft gestart. Voor deze zelfstudie is de actie het verzenden van een e-mailmelding via uw e-mailprovider. 

1. Selecteer **Nieuwe stap**. Hiermee wordt een venster geopend om **een actie**te kiezen .

1. Zoek naar **e-mail**.

1. Zoek en selecteer de bijbehorende connector op basis van uw e-mailprovider. In deze zelfstudie wordt **Office 365 Outlook gebruikt.** De stappen voor andere e-mailproviders zijn vergelijkbaar. 

   ![Connector voor e-mailprovider selecteren](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Selecteer de actie **Een e-mail verzenden**. 

1. Meld u aan bij uw e-mailaccount als dat wordt gevraagd. 

1. Stel de e-mailsjabloon samen. 

   * **Aan**: voer het e-mailadres in waarop u de e-mailmeldingen wilt ontvangen. Gebruik voor deze zelfstudie een e-mailaccount dat toegankelijk is voor testdoeleinden. 

   * **Onderwerp**: Vul de tekst voor het onderwerp in. Wanneer u op het tekstvak Onderwerp klikt, u dynamische inhoud selecteren die u wilt opnemen. In deze zelfstudie `IoT Hub alert: {event Type}`wordt bijvoorbeeld gebruik gemaakt van . Als u dynamische inhoud niet zien, selecteert u de hyperlink **Dynamische inhoud toevoegen:** hiermee schakelt u deze in en uit.

   * **Hoofdtekst**: Schrijf de tekst voor uw e-mail. Selecteer JSON-eigenschappen in het selectiehulpmiddel om dynamische inhoud op te nemen op basis van gegevens van gebeurtenissen. Als u de dynamische inhoud niet zien, selecteert u de hyperlink **Dynamische inhoud toevoegen** onder het tekstvak Tekst van de tekst van de **hoofdtekst.** Als u niet de gewenste velden laat zien, klikt u *meer* in het scherm Dynamische inhoud om de velden uit de vorige actie op te nemen.

   Uw e-mailsjabloon ziet er nu misschien uit als in dit voorbeeld:

   ![Informatie voor e-mail invullen](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. Sla uw logische app op. 

### <a name="copy-the-http-url"></a>HTTP-URL kopiëren

Voordat u de functie Ontwerper van logische apps verlaat, kopieert u de URL waarnaar uw logische app luistert voor een trigger. U gebruikt deze URL voor het configureren van Event Grid. 

1. Vouw het configuratievak **Wanneer een HTTP-aanvraag is ontvangen** uit door erop te klikken. 

1. Kopieer de waarde van **URL voor HTTP POST** door de knop URL kopiëren ernaast te selecteren. 

   ![De URL voor HTTP POST kopiëren](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Sla deze URL op zodat u ernaar kunt verwijzen in de volgende sectie. 

## <a name="configure-subscription-for-iot-hub-events"></a>Abonnement voor IoT Hub-gebeurtenissen configureren

In deze sectie configureert u de IoT-hub voor het publiceren van gebeurtenissen op het moment dat deze optreden. 

1. Ga in Azure Portal naar uw IoT-hub. U dit doen door **Resourcegroepen te**selecteren, vervolgens de resourcegroep voor deze zelfstudie te selecteren en vervolgens uw IoT-hub te selecteren in de lijst met bronnen.

2. Selecteer **Gebeurtenissen**.

   ![Details van gebeurtenisraster weergeven](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Selecteer **Abonnement op evenement**. 

   ![Nieuw gebeurtenisabonnement maken](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Maak het gebeurtenisabonnement met de volgende waarden: 

   * **Details van het evenementabonnement:** geef een beschrijvende naam op en selecteer **gebeurtenisrasterschema**.

   * **Gebeurtenistypen:** schakel in de **filter naar gebeurtenistypen**alle opties uit, behalve **Apparaat gemaakt.**

       ![gebeurtenistypen voor abonnementen](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)

   * **Eindpuntdetails**: Selecteer Eindpunttype als **webhaak** en *selecteer een eindpunt* en plak de URL die u uit uw logische app hebt gekopieerd en bevestig selectie.

     ![eindpunt-URL selecteren](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

   Wanneer u klaar bent, moet het deelvenster er als volgt uitzien: 

    ![Voorbeeld van formulier voor gebeurtenisabonnement](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. U kunt het gebeurtenisabonnement nu opslaan en dan meldingen ontvangen voor elk apparaat dat wordt gemaakt in uw IoT-hub. Voor deze zelfstudie gaan we echter de optionele velden gebruiken om te filteren op specifieke apparaten. Selecteer **Filters** boven aan het deelvenster.

6. Selecteer **Nieuw filter toevoegen**. Vul de velden in met de volgende waarden:

   * **Sleutel**: `Subject`Selecteer .

   * **Operator**: `String begins with`Selecteer .

   * **Waarde:** `devices/Building1_` Voer in om te filteren op apparaatgebeurtenissen in gebouw 1.
  
   Voeg een ander filter toe met de volgende waarden:

   * **Sleutel**: `Subject`Selecteer .

   * **Operator**: `String ends with`Selecteer .

   * **Waarde:** `_Temperature` Voer in om te filteren op apparaatgebeurtenissen die verband houden met temperatuur.

   Het tabblad **Filters** van uw evenementabonnement moet er nu op deze afbeelding uitzien:

   ![Filters toevoegen aan gebeurtenisabonnement](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

7. Selecteer **Maken** om het gebeurtenisabonnement op te slaan.

## <a name="create-a-new-device"></a>Een nieuw apparaat maken

Test de logische app door een nieuw apparaat te maken om zo een e-mail voor melding van een gebeurtenis te triggeren. 

1. Selecteer **IoT-apparaten** in de IoT-hub. 

2. Selecteer **Nieuw**.

3. Geef `Building1_Floor1_Room1_Light` op voor **Apparaat-ID**.

4. Selecteer **Opslaan**. 

5. U kunt meerdere apparaten met verschillende apparaat-id's toevoegen om de filters voor het gebeurtenisabonnement te testen. Probeer deze voorbeelden: 

   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

   Als u de vier voorbeelden hebt toegevoegd, moet uw lijst met IoT-apparaten er als de volgende afbeelding uitzien:

   ![Lijst met IoT-hub-apparaten](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. Als u een paar apparaten hebt toegevoegd aan uw IoT-hub, controleert u of er e-mail is om te zien welke acties de logische app hebben geactiveerd. 

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

In plaats van Azure Portal te gebruiken, kunt u de stappen voor IoT Hub ook uitvoeren met de Azure CLI. Zie de Azure CLI-pagina's voor [het maken van een gebeurtenisabonnement](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) en het maken van een [IoT-apparaat](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity)voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

In deze zelfstudie zijn resources gebruikt die kosten voor uw Azure-abonnement met zich meebrengen. Wanneer u klaar bent met het uitproberen van de zelfstudie en het testen van uw resultaten, schakelt u resources uit of verwijdert die u niet wilt behouden. 

Als u alle bronnen wilt verwijderen die in deze zelfstudie zijn gemaakt, verwijdert u de brongroep. 

1. Selecteer **Resourcegroepen**en selecteer vervolgens de resourcegroep die u voor deze zelfstudie hebt gemaakt.

2. Selecteer **resourcegroep verwijderen**in het groepsvenster Resource . U wordt gevraagd de naam van de brongroep in te voeren en deze vervolgens te verwijderen. Alle daarin opgenomen middelen worden ook verwijderd.

Als u niet alle resources wilt verwijderen, u ze één voor één beheren. 

Als u het werk aan uw logische app wilt behouden, kunt u de app uitschakelen in plaats van verwijderen. 

1. Ga naar uw logische app.

2. Selecteer **Verwijderen** of **Uitschakelen** op de blade **Overzicht**. 

Elk abonnement biedt toegang tot één gratis IoT-hub. Als u een gratis hub hebt gemaakt voor deze zelfstudie, hoeft u deze niet te verwijderen om te voorkomen dat er kosten in rekening worden gebracht.

1. Ga naar uw IoT-hub. 

2. Selecteer **Verwijderen** op de blade **Overzicht**. 

Zelfs als u uw IoT-hub wilt behouden, kunt u het gebeurtenisabonnement verwijderen dat u hebt gemaakt. 

1. Selecteer hiervoor **Event Grid** in uw IoT-hub.

2. Selecteer het gebeurtenisabonnement dat u wilt verwijderen. 

3. Selecteer **Verwijderen**. 

## <a name="next-steps"></a>Volgende stappen

* Ga voor meer informatie naar [Reageren op gebeurtenissen van IoT Hub door met behulp van Event Grid acties te triggeren - Preview](../iot-hub/iot-hub-event-grid.md).
* [Informatie over het rangschikken van gebeurtenissen ‘apparaat verbonden’ en ‘verbinding met apparaat verbroken’.](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Lees hier meer over wat u kunt doen met [Event Grid](overview.md).
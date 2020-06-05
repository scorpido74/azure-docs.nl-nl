---
title: 'Zelfstudie: Azure Logic Apps triggeren met IoT Hub-gebeurtenissen'
description: In deze zelfstudie leert u hoe u de functie voor het routeren van gebeurtenissen van Azure Event Grid gebruikt voor het maken van geautomatiseerde processen om acties van Azure Logic Apps uit te voeren op basis van IoT Hub-gebeurtenissen.
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: robinsh
ms.openlocfilehash: 0b1870af6316713590eec59aee2af94ce34b7e1a
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722555"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Zelfstudie: E-mailmeldingen over gebeurtenissen van Azure IoT Hub verzenden met Event Grid en Logic Apps

Azure Event Grid maakt het mogelijk om te reageren op gebeurtenissen in IoT Hub door acties in zakelijke toepassingen verderop in de werkstroom te activeren.

In dit artikel wordt u stapsgewijs begeleid bij het maken van een voorbeeldconfiguratie waarin IoT Hub en Event Grid worden gebruikt. Aan het einde van het artikel beschikt u over een logische Azure-app die een e-mailmelding verstuurt wanneer er een apparaat wordt toegevoegd aan uw IoT-hub. 

## <a name="prerequisites"></a>Vereisten

* Een actief Azure-abonnement. Als u geen abonnement hebt, kunt u [een gratis Azure-account maken](https://azure.microsoft.com/pricing/free-trial/).

* Een e-mailaccount van een e-mailprovider die door Azure Logic Apps wordt ondersteund, bijvoorbeeld Office 365 Outlook, Outlook.com of Gmail. Dit e-mailaccount wordt gebruikt voor het verzenden van de gebeurtenismeldingen. Zie [Overzicht van connectors](https://docs.microsoft.com/connectors/) voor een volledige lijst met ondersteunde Logic App-connectors.

  > [!IMPORTANT]
  > Voordat u Gmail gebruikt, controleert u of u een G Suite-bedrijfsaccount hebt (e-mailadres met een aangepast domein) of een Gmail-consumentenaccount (e-mailadres met @gmail.com of @googlemail.com). Alleen bedrijfsaccounts van G Suite kunnen de Gmail-connector zonder beperkingen gebruiken met andere connectors in logische apps. Als u een Gmail-consumentenaccount hebt, kunt u de Gmail-connector alleen gebruiken met specifieke door Google goedgekeurde services, of u kunt [een Google-client-app maken voor verificatie](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application). Zie [Beleid voor gegevensbeveiliging en privacybeleid voor Google-connectors in Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md) voor meer informatie.

* Een IoT Hub in Azure. Als u nog geen hub hebt gemaakt, leest u [Get started with IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md) (Aan de slag met IoT Hub) voor stapsgewijze instructies.

## <a name="create-a-logic-app"></a>Een logische app maken

U gaat eerst een logische app maken en een trigger voor het gebeurtenisraster toevoegen die de resourcegroep voor uw virtuele machine bewaakt. 

### <a name="create-a-logic-app-resource"></a>Een logische app maken

1. Selecteer in de [Azure Portal](https://portal.azure.com) **Een resource maken**, typ ' logische app ' in het zoekvak en selecteer Enter. Selecteer **Logische app** in de resultaten.

   ![Logische app maken](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. Selecteer **Maken** in het volgende scherm. 

1. Geef een naam op voor de logische app die uniek is in uw abonnement en selecteer vervolgens het abonnement, de resourcegroep en de locatie van uw IoT-hub. 

   ![Velden voor het maken van logische apps](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Selecteer **Maken**.

1. Als de resource is gemaakt, navigeert u naar uw logische app. Selecteer hiervoor **Resourcegroepen** en selecteer vervolgens de resourcegroep die u hebt gemaakt voor deze zelfstudie. Zoek en selecteer de logische app in de resourcelijst. 

1. Blader omlaag in de Ontwerper van logische apps naar **Sjablonen**. Kies **Lege logische app**, zodat u de logische app helemaal zelf kunt ontwerpen.

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

1. Selecteer **Nieuwe stap**. **Kies een actie** in het venster dat wordt geopend.

1. Zoek naar **e-mail**.

1. Zoek en selecteer de bijbehorende connector op basis van uw e-mailprovider. In deze zelfstudie wordt **Office 365 Outlook** gebruikt. De stappen voor andere e-mailproviders zijn vergelijkbaar. 

   ![Connector voor e-mailprovider selecteren](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Selecteer de actie **Een e-mail verzenden**. 

1. Meld u aan bij uw e-mailaccount als dat wordt gevraagd. 

1. Stel de e-mailsjabloon samen. 

   * **Aan**: Voer het e-mailadres in waarop u de e-mailmeldingen wilt ontvangen. Gebruik voor deze zelfstudie een e-mailaccount dat toegankelijk is voor testdoeleinden. 

   * **Onderwerp**: Vul de tekst voor het onderwerp in. Wanneer u op het tekstvak Onderwerp klikt, kunt u dynamische inhoud selecteren die u wilt toevoegen. In deze zelfstudie wordt bijvoorbeeld `IoT Hub alert: {event Type}` gebruikt. Als u geen dynamische inhoud kunt zien, selecteert u de hyperlink **Dynamische inhoud toevoegen**, waarmee u deze optie in- en uitschakelt.

   * **Hoofdtekst**: Typ hier het onderwerp en de tekst voor uw e-mail. Selecteer JSON-eigenschappen in het selectiehulpmiddel om dynamische inhoud op te nemen op basis van gegevens van gebeurtenissen. Als u de dynamische inhoud niet kunt zien, selecteert u de hyperlink **Dynamische inhoud toevoegen** onder het tekstvak **Hoofdtekst**. Als de velden die u wilt gebruiken niet worden weergegeven, klikt u op *Meer* in het scherm Dynamische inhoud om de velden van de vorige actie op te nemen.

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

1. Ga in Azure Portal naar uw IoT-hub. U kunt dit doen door **Resourcegroepen** te selecteren en vervolgens de resourcegroep voor deze zelfstudie te selecteren. Selecteer vervolgens uw IoT-hub in de lijst met resources.

2. Selecteer **Gebeurtenissen**.

   ![Details van gebeurtenisraster weergeven](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Selecteer **Gebeurtenisabonnement**. 

   ![Nieuw gebeurtenisabonnement maken](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Maak het gebeurtenisabonnement met de volgende waarden: 

   * **Gebeurtenisabonnementdetails**: Geef een beschrijvende naam op en selecteer **Event Grid-schema**.

   * **Gebeurtenistypen**: Schakel in **Filteren op gebeurtenistypen** alle keuzes uit behalve **Apparaat is gemaakt**.

       ![gebeurtenistypen abonnement](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)

   * **Eindpuntdetails**: Selecteer Eindpunttype als **Webhook**, selecteer *Eindpunt selecteren*, plak de URL die u hebt gekopieerd in uw logische app en bevestig uw selectie.

     ![eindpunt-URL selecteren](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

   Als u klaar bent, moet het deelvenster er als volgt uitzien: 

    ![Voorbeeld van formulier voor gebeurtenisabonnement](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. U kunt het gebeurtenisabonnement nu opslaan en dan meldingen ontvangen voor elk apparaat dat wordt gemaakt in uw IoT-hub. Voor deze zelfstudie gaan we echter de optionele velden gebruiken om te filteren op specifieke apparaten. Selecteer **Filters** bovenaan het deelvenster.

6. Selecteer **Nieuw filter toevoegen**. Vul de velden in met deze waarden:

   * **Sleutel**: Selecteer `Subject`.

   * **Operator**: Selecteer `String begins with`.

   * **Waarde**:  Voer `devices/Building1_` in om te filteren op apparaatgebeurtenissen in gebouw 1.
  
   Voeg nog een filter toe met deze waarden:

   * **Sleutel**: Selecteer `Subject`.

   * **Operator**: Selecteer `String ends with`.

   * **Waarde**: Voer `_Temperature` in om te filteren op apparaatgebeurtenissen die te maken hebben met temperatuur.

   Het tabblad **Filters** van uw gebeurtenisabonnement moet er nu ongeveer als volgt uitzien:

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

   Als u de vier voorbeelden hebt toegevoegd, ziet uw lijst van IoT-apparaten er ongeveer als volgt uit:

   ![Lijst met IoT Hub-apparaten](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. Als u een paar apparaten hebt toegevoegd aan uw IoT-hub, controleert u of er e-mail is om te zien welke acties de logische app hebben geactiveerd. 

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

In plaats van Azure Portal te gebruiken, kunt u de stappen voor IoT Hub ook uitvoeren met de Azure CLI. Zie de Azure CLI-pagina's voor het [maken van een gebeurtenisabonnement](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) en het [maken van een IoT-apparaat](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity) voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

In deze zelfstudie zijn resources gebruikt die kosten voor uw Azure-abonnement met zich meebrengen. Wanneer u klaar bent met de zelfstudie en het testen van de resultaten, moet u daarom de resources uitschakelen of verwijderen die u niet wilt behouden. 

Als u alle resources wilt verwijderen die u in deze zelfstudie hebt gemaakt, verwijdert u de resourcegroep. 

1. Selecteer **Resourcegroepen** en selecteer vervolgens de resourcegroep die u hebt gemaakt voor deze zelfstudie.

2. Selecteer **Resourcegroep verwijderen** op het deelvenster Resourcegroep. U wordt gevraagd de naam van de resourcegroep op te geven, waarna u deze kunt verwijderen. Alle resources in de resourcegroep worden ook verwijderd.

Als u niet alle resources wilt verwijderen, moet u ze afzonderlijk beheren. 

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
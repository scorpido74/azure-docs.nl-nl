---
title: 'Zelf studie: IoT Hub gebeurtenissen gebruiken om Azure Logic Apps te activeren'
description: 'Zelf studie: de service voor gebeurtenis routering van Azure Event Grid gebruiken om geautomatiseerde processen te maken om Azure Logic Apps acties uit te voeren op basis van IoT Hub gebeurtenissen.'
services: iot-hub
documentationcenter: ''
author: kgremban
manager: philmea
editor: ''
ms.service: iot-hub
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: kgremban
ms.openlocfilehash: e003cb650b0589ab43c984850838c56cbbf1ff2f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106771"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-logic-apps"></a>Zelf studie: e-mail meldingen over Azure IoT Hub-gebeurtenissen verzenden met Logic Apps

Azure Event Grid maakt het mogelijk om te reageren op gebeurtenissen in IoT Hub door acties in zakelijke toepassingen verderop in de werkstroom te activeren.

In dit artikel wordt u stapsgewijs begeleid bij het maken van een voorbeeldconfiguratie waarin IoT Hub en Event Grid worden gebruikt. Aan het einde van het artikel beschikt u over een logische Azure-app die een e-mailmelding verstuurt wanneer er een apparaat wordt toegevoegd aan uw IoT-hub. 

## <a name="prerequisites"></a>Vereisten

* Een e-mailaccount van een e-mailprovider die door Azure Logic Apps wordt ondersteund, bijvoorbeeld Office 365 Outlook, Outlook.com of Gmail. Dit e-mailaccount wordt gebruikt voor het verzenden van de gebeurtenismeldingen. Zie [Overzicht van connectors](https://docs.microsoft.com/connectors/) voor een volledige lijst met ondersteunde Logic App-connectors.
* Een actief Azure-account. Als u nog geen account hebt, kunt u [een gratis account aanmaken](https://azure.microsoft.com/pricing/free-trial/).
* Een IoT Hub in Azure. Als u nog geen hub hebt gemaakt, leest u [Get started with IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md) (Aan de slag met IoT Hub) voor stapsgewijze instructies. 

## <a name="create-a-logic-app"></a>Een logische app maken

U gaat eerst een logische app maken en een trigger voor Event Grid toevoegen die de resourcegroep voor uw virtuele machine bewaakt. 

### <a name="create-a-logic-app-resource"></a>Een logische app maken

1. Selecteer in [Azure Portal](https://portal.azure.com) **Een resource maken** > **Integratie** > **Logische app**.

   ![Logische app maken](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

2. Geef een naam op voor de logische app die uniek is in uw abonnement en selecteer vervolgens het abonnement, de resourcegroep en de locatie van uw IoT-hub. 
3. Selecteer **Maken**.

4. Als de resource is gemaakt, navigeert u naar uw logische app. 

5. In de ontwerper van Logic Apps worden sjablonen voor veelvoorkomende patronen weergegeven zodat u sneller aan de slag kunt. Kies in Ontwerper van logische apps onder **Sjablonen** de optie **Lege logische app**, zodat u de logische app helemaal zelf kunt ontwerpen.

### <a name="select-a-trigger"></a>Een trigger selecteren

Een trigger is een specifieke gebeurtenis waarmee uw logische app wordt gestart. Voor deze zelfstudie is de trigger voor het activeren van de werkstroom het ontvangen van een aanvraag via HTTP.  

1. Typ **HTTP** in de zoekbalk voor connectors en triggers.
2. Selecteer **Aanvraag - Wanneer een HTTP-aanvraag is ontvangen** als de trigger. 

   ![Selecteer de trigger Aanvraag - Wanneer een HTTP-aanvraag is ontvangen](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

3. Selecteer **Voorbeeldnettolading om een schema te genereren**. 

   ![Selecteer de trigger Aanvraag - Wanneer een HTTP-aanvraag is ontvangen](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

4. Plak de volgende voorbeeldcode van JSON in het tekstvak en selecteer vervolgens **Gereed**:

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

5. Er kan een melding worden weergegeven dat u niet moet vergeten **in uw aanvraag een header Content-type op te nemen die is ingesteld op application/json**. U kunt deze suggestie zonder problemen negeren en verdergaan met de volgende sectie. 

### <a name="create-an-action"></a>Een actie maken

Acties zijn stappen die worden uitgevoerd nadat de trigger de werkstroom van de logische app heeft gestart. Voor deze zelfstudie is de actie het verzenden van een e-mailmelding via uw e-mailprovider. 

1. Selecteer **Nieuwe stap**. **Kies een actie** in het venster dat wordt geopend.

2. Zoek naar **e-mail**.

3. Zoek en selecteer de bijbehorende connector op basis van uw e-mailprovider. In deze zelfstudie wordt **Office 365 Outlook** gebruikt. De stappen voor andere e-mailproviders zijn vergelijkbaar. 

   ![Connector voor e-mailprovider selecteren](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

4. Selecteer de actie **Een e-mail verzenden**. 

5. Meld u aan bij uw e-mailaccount als dat wordt gevraagd. 

6. Stel de e-mailsjabloon samen. 
   * **Aan**: voer het e-mailadres in waarop u de e-mailmeldingen wilt ontvangen. Gebruik voor deze zelfstudie een e-mailaccount dat toegankelijk is voor testdoeleinden. 
   * **Onderwerp** en **Hoofdtekst**: typ hier het onderwerp en de tekst voor uw e-mail. Selecteer JSON-eigenschappen in het selectiehulpmiddel om dynamische inhoud op te nemen op basis van gegevens van gebeurtenissen.  

   Uw e-mailsjabloon ziet er nu misschien uit als in dit voorbeeld:

   ![Informatie voor e-mail invullen](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

7. Sla uw logische app op. 

### <a name="copy-the-http-url"></a>HTTP-URL kopiëren

Voordat u de functie Ontwerper van logische apps verlaat, kopieert u de URL waarnaar uw logische app luistert voor een trigger. U gebruikt deze URL voor het configureren van Event Grid. 

1. Vouw het configuratievak **Wanneer een HTTP-aanvraag is ontvangen** uit door erop te klikken. 
2. Kopieer de waarde van **URL voor HTTP POST** door de knop URL kopiëren ernaast te selecteren. 

   ![De URL voor HTTP POST kopiëren](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

3. Sla deze URL op zodat u ernaar kunt verwijzen in de volgende sectie. 

## <a name="configure-subscription-for-iot-hub-events"></a>Abonnement voor IoT Hub-gebeurtenissen configureren

In deze sectie configureert u de IoT-hub voor het publiceren van gebeurtenissen op het moment dat deze optreden. 

1. Ga in Azure Portal naar uw IoT-hub. 
2. Selecteer **Gebeurtenissen**.

   ![Details van gebeurtenisraster weergeven](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Selecteer **Gebeurtenisabonnement**. 

   ![Nieuw gebeurtenisabonnement maken](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Maak het gebeurtenisabonnement met de volgende waarden: 
   * **Gebeurtenistype**: schakel Abonneren op alle gebeurtenistypen uit en selecteer **Het apparaat is gemaakt** in het menu.
   * **Eindpuntdetails**: selecteer Eindpunttype als **Webhook**, klik op Eindpunt selecteren, plak de URL die u hebt gekopieerd in uw logische app en bevestig uw selectie.

     ![eindpunt-URL selecteren](./media/publish-iot-hub-events-to-logic-apps/endpoint-url.png)

   * **Gebeurtenisabonnementdetails**: geef een beschrijvende naam op en selecteer **Gebeurtenisrasterschema**.

   Als u klaar bent, moet het formulier er als volgt uitzien: 

    ![Voorbeeld van formulier voor gebeurtenisabonnement](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. U kunt het gebeurtenisabonnement nu opslaan en dan meldingen ontvangen voor elk apparaat dat wordt gemaakt in uw IoT-hub. Voor deze zelfstudie gaan we echter de optionele velden gebruiken om te filteren op specifieke apparaten. Selecteer **Aanvullende functies** boven aan het formulier. 

6. Maak de volgende filters:

   * **Onderwerp begint met**: voer `devices/Building1_` in om te filteren op apparaatgebeurtenissen in gebouw 1.
   * **Onderwerp eindigt met**: voer `_Temperature` in om te filteren op apparaatgebeurtenissen die te maken hebben met temperatuur.

5. Selecteer **Maken** om het gebeurtenisabonnement op te slaan.

## <a name="create-a-new-device"></a>Een nieuw apparaat maken

Test de logische app door een nieuw apparaat te maken om zo een e-mail voor melding van een gebeurtenis te triggeren. 

1. Selecteer **IoT-apparaten** in de IoT-hub. 
2. Selecteer **Toevoegen**.
3. Geef **op voor**Apparaat-ID`Building1_Floor1_Room1_Temperature`.
4. Selecteer **Opslaan**. 
5. U kunt meerdere apparaten met verschillende apparaat-id's toevoegen om de filters voor het gebeurtenisabonnement te testen. Probeer deze voorbeelden: 
   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

Als u een paar apparaten hebt toegevoegd aan uw IoT-hub, controleert u of er e-mail is om te zien welke acties de logische app hebben geactiveerd. 

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

In plaats van Azure Portal te gebruiken, kunt u de stappen voor IoT Hub ook uitvoeren met de Azure CLI. Zie de Azure CLI-pagina's voor het [maken van een gebeurtenisabonnement](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) en het [maken van een IoT-apparaat](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity) voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

In deze zelfstudie zijn resources gebruikt die kosten voor uw Azure-abonnement met zich meebrengen. Wanneer u klaar bent met de zelfstudie en het testen van de resultaten, moet u daarom de resources uitschakelen of verwijderen die u niet wilt behouden. 

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



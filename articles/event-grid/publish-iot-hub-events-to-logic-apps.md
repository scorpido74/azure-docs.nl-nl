---
title: Zelf studie-IoT Hub gebeurtenissen gebruiken om Azure Logic Apps te activeren
description: In deze zelf studie ziet u hoe u de service voor gebeurtenis routering van Azure Event Grid kunt gebruiken om geautomatiseerde processen te maken om Azure Logic Apps acties op basis van IoT Hub gebeurtenissen uit te voeren.
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: robinsh
ms.openlocfilehash: 889c5e68759a94682150ac88970b7123ad0fc412
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82201734"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Zelf studie: e-mail meldingen over Azure IoT Hub-gebeurtenissen verzenden met behulp van Event Grid en Logic Apps

Azure Event Grid maakt het mogelijk om te reageren op gebeurtenissen in IoT Hub door acties in zakelijke toepassingen verderop in de werkstroom te activeren.

In dit artikel wordt een voorbeeld configuratie beschreven die gebruikmaakt van IoT Hub en Event Grid. Aan het einde hebt u een Azure Logic app ingesteld voor het verzenden van een e-mail melding wanneer een apparaat wordt toegevoegd aan uw IoT-hub. 

## <a name="prerequisites"></a>Vereisten

* Een actief Azure-abonnement. Als u geen abonnement hebt, kunt u [een gratis Azure-account maken](https://azure.microsoft.com/pricing/free-trial/).

* Een e-mail account van een e-mail provider die wordt ondersteund door Azure Logic Apps, zoals Office 365 Outlook, Outlook.com of Gmail. Dit e-mailaccount wordt gebruikt voor het verzenden van de gebeurtenismeldingen. Zie het [overzicht connectors](https://docs.microsoft.com/connectors/)voor een volledige lijst met ondersteunde logische app-connectors.

  > [!IMPORTANT]
  > Voordat u Gmail gebruikt, controleert u of u een G-suite-bedrijfs account hebt (e-mail adres met een aangepast domein) of een Gmail-consument @gmail.com account @googlemail.com(e-mail adres met of). Alleen zakelijke accounts van G-suite kunnen de Gmail-connector gebruiken met andere connectors zonder beperkingen in Logic apps. Als u een Gmail-consument account hebt, kunt u de Gmail-connector gebruiken met alleen specifieke door Google goedgekeurde Services, of kunt u [een Google-client-app maken die voor verificatie moet worden gebruikt](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application). Zie voor meer informatie [beleid voor gegevens beveiliging en privacybeleid voor Google connectors in azure Logic apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Een IoT Hub in Azure. Als u nog geen hub hebt gemaakt, leest u [Get started with IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md) (Aan de slag met IoT Hub) voor stapsgewijze instructies.

## <a name="create-a-logic-app"></a>Een logische app maken

Eerst maakt u een logische app en voegt u een event grid-trigger toe waarmee de resource groep voor uw virtuele machine wordt gecontroleerd. 

### <a name="create-a-logic-app-resource"></a>Een logische app maken

1. Selecteer in het [Azure Portal](https://portal.azure.com)de optie **een resource maken**, typ ' logische app ' in het zoekvak en selecteer vervolgens retour. Selecteer **logische app** uit de resultaten.

   ![Logische app maken](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. Selecteer in het volgende scherm **maken**. 

1. Geef een naam op voor de logische app die uniek is in uw abonnement en selecteer vervolgens het abonnement, de resourcegroep en de locatie van uw IoT-hub. 

   ![Velden voor het maken van logische apps](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Selecteer **Maken**.

1. Als de resource is gemaakt, navigeert u naar uw logische app. Hiervoor selecteert u **resource groepen**en selecteert u vervolgens de resource groep die u hebt gemaakt voor deze zelf studie. Zoek vervolgens de logische app in de lijst met resources en selecteer deze. 

1. Ga in de Logic Apps Designer naar pagina's om **sjablonen**weer te geven. Kies **lege logische app** , zodat u de logische app helemaal zelf kunt bouwen.

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

1. Selecteer **Nieuwe stap**. Hiermee opent u een venster om **een actie te kiezen**.

1. Zoek naar **e-mail**.

1. Zoek en selecteer de bijbehorende connector op basis van uw e-mailprovider. In deze zelf studie wordt **Office 365 Outlook**gebruikt. De stappen voor andere e-mailproviders zijn vergelijkbaar. 

   ![Connector voor e-mailprovider selecteren](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Selecteer de actie **Een e-mail verzenden**. 

1. Meld u aan bij uw e-mailaccount als dat wordt gevraagd. 

1. Stel de e-mailsjabloon samen. 

   * **Aan**: voer het e-mailadres in waarop u de e-mailmeldingen wilt ontvangen. Gebruik voor deze zelfstudie een e-mailaccount dat toegankelijk is voor testdoeleinden. 

   * **Onderwerp**: Vul de tekst voor het onderwerp in. Wanneer u op het tekstvak onderwerp klikt, kunt u dynamische inhoud selecteren die u wilt toevoegen. Deze zelf studie maakt bijvoorbeeld gebruik `IoT Hub alert: {event Type}`van. Als u geen dynamische inhoud kunt zien, selecteert u de Hyper link **dynamische inhoud toevoegen** --Hiermee schakelt u deze in en uit.

   * **Tekst: Schrijf**de tekst voor uw e-mail adres. Selecteer JSON-eigenschappen in het selectiehulpmiddel om dynamische inhoud op te nemen op basis van gegevens van gebeurtenissen. Als u de dynamische inhoud niet kunt zien, selecteert u de Hyper link **dynamische inhoud toevoegen** onder het tekstvak **hoofd** tekst. Als niet de gewenste velden worden weer gegeven, klikt u op *meer* in het scherm dynamische inhoud om de velden van de vorige actie op te geven.

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

1. Ga in Azure Portal naar uw IoT-hub. U kunt dit doen door **resource groepen**te selecteren en vervolgens de resource groep voor deze zelf studie te selecteren en vervolgens uw IOT-hub te selecteren in de lijst met resources.

2. Selecteer **gebeurtenissen**.

   ![Details van gebeurtenisraster weergeven](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Selecteer een **gebeurtenis abonnement**. 

   ![Nieuw gebeurtenisabonnement maken](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Maak het gebeurtenisabonnement met de volgende waarden: 

   * **Details van gebeurtenis abonnementen**: Geef een beschrijvende naam op en selecteer **Event grid schema**.

   * **Gebeurtenis typen**: Schakel in het **filter voor gebeurtenis typen**alle opties uit behalve het apparaat dat is **gemaakt**.

       ![abonnements gebeurtenis typen](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)

   * **Eindpunt Details**: Selecteer het type eind punt als een **webhook** en selecteer *een eind punt selecteren* en plak de URL die u hebt gekopieerd uit uw logische app en bevestig de selectie.

     ![eindpunt-URL selecteren](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

   Wanneer u klaar bent, ziet het deel venster eruit als in het volgende voor beeld: 

    ![Voorbeeld van formulier voor gebeurtenisabonnement](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. U kunt het gebeurtenisabonnement nu opslaan en dan meldingen ontvangen voor elk apparaat dat wordt gemaakt in uw IoT-hub. Voor deze zelfstudie gaan we echter de optionele velden gebruiken om te filteren op specifieke apparaten. Selecteer **filters** boven aan het deel venster.

6. Selecteer **Nieuw filter toevoegen**. Vul de velden in met deze waarden:

   * **Sleutel**: selecteren `Subject`.

   * **Operator**: selecteren `String begins with`.

   * **Waarde**: Voer `devices/Building1_` in om te filteren op faxgebeurtenissen in gebouw 1.
  
   Voeg nog een filter toe met deze waarden:

   * **Sleutel**: selecteren `Subject`.

   * **Operator**: selecteren `String ends with`.

   * **Waarde**: Voer `_Temperature` dit in om te filteren op gebeurtenissen die betrekking hebben op de Tempe ratuur.

   Het tabblad **filters** van uw gebeurtenis abonnement moet er nu ongeveer als volgt uitzien:

   ![Filters toevoegen aan het gebeurtenis abonnement](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

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

   Als u de vier voor beelden hebt toegevoegd, moet uw lijst met IoT-apparaten eruitzien als de volgende afbeelding:

   ![IoT Hub apparaten lijst](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. Als u een paar apparaten hebt toegevoegd aan uw IoT-hub, controleert u of er e-mail is om te zien welke acties de logische app hebben geactiveerd. 

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

In plaats van Azure Portal te gebruiken, kunt u de stappen voor IoT Hub ook uitvoeren met de Azure CLI. Zie de Azure CLI-pagina's voor het [maken van een gebeurtenis abonnement](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) en het [maken van een IOT-apparaat](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity)voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

In deze zelfstudie zijn resources gebruikt die kosten voor uw Azure-abonnement met zich meebrengen. Wanneer u klaar bent met het uitproberen van de zelf studie en de resultaten test, kunt u resources die u niet wilt hand haven, uitschakelen of verwijderen. 

Als u alle resources die in deze zelf studie zijn gemaakt wilt verwijderen, verwijdert u de resource groep. 

1. Selecteer **resource groepen**en selecteer vervolgens de resource groep die u hebt gemaakt voor deze zelf studie.

2. Selecteer **resource groep verwijderen**in het deel venster Resource groep. U wordt gevraagd de naam van de resource groep op te geven. u kunt deze vervolgens verwijderen. Alle resources in de resource worden ook verwijderd.

Als u niet alle resources wilt verwijderen, kunt u ze één voor één beheren. 

Als u het werk aan uw logische app wilt behouden, kunt u de app uitschakelen in plaats van verwijderen. 

1. Ga naar uw logische app.

2. Selecteer **Verwijderen** of **Uitschakelen** op de blade **Overzicht**. 

Elk abonnement biedt toegang tot één gratis IoT-hub. Als u een gratis hub hebt gemaakt voor deze zelfstudie, hoeft u deze niet te verwijderen om te voorkomen dat er kosten in rekening worden gebracht.

1. Ga naar uw IoT-hub. 

2. Selecteer **Verwijderen** op de blade **Overzicht**. 

Zelfs als u uw IoT-hub wilt behouden, kunt u het gebeurtenisabonnement verwijderen dat u hebt gemaakt. 

1. Selecteer hiervoor **Event Grid** in uw IoT-hub.

2. Selecteer het gebeurtenisabonnement dat u wilt verwijderen. 

3. Selecteer **verwijderen**. 

## <a name="next-steps"></a>Volgende stappen

* Ga voor meer informatie naar [Reageren op gebeurtenissen van IoT Hub door met behulp van Event Grid acties te triggeren - Preview](../iot-hub/iot-hub-event-grid.md).
* [Informatie over het rangschikken van gebeurtenissen ‘apparaat verbonden’ en ‘verbinding met apparaat verbroken’.](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Lees hier meer over wat u kunt doen met [Event Grid](overview.md).
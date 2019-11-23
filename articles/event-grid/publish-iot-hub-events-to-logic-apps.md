---
title: Tutorial - Use IoT Hub events to trigger Azure Logic Apps
description: Tutorial - Using the event routing service of Azure Event Grid, create automated processes to perform Azure Logic Apps actions based on IoT Hub events.
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: robinsh
ms.openlocfilehash: 70ad74715446a54605a23a049ebc92a81d7ee673
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423740"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Tutorial: Send email notifications about Azure IoT Hub events using Event Grid and Logic Apps

Azure Event Grid maakt het mogelijk om te reageren op gebeurtenissen in IoT Hub door acties in zakelijke toepassingen verderop in de werkstroom te activeren.

This article walks through a sample configuration that uses IoT Hub and Event Grid. At the end, you have an Azure logic app set up to send a notification email every time a device is added to your IoT hub. 

## <a name="prerequisites"></a>Vereisten

* Een e-mailaccount van een e-mailprovider die door Azure Logic Apps wordt ondersteund, bijvoorbeeld Office 365 Outlook, Outlook.com of Gmail. Dit e-mailaccount wordt gebruikt voor het verzenden van de gebeurtenismeldingen. Zie [Overzicht van connectors](https://docs.microsoft.com/connectors/) voor een volledige lijst met ondersteunde Logic App-connectors.
* Een actief Azure-account. Als u nog geen account hebt, kunt u [een gratis account aanmaken](https://azure.microsoft.com/pricing/free-trial/).
* Een IoT Hub in Azure. Als u nog geen hub hebt gemaakt, leest u [Get started with IoT Hub](../iot-hub/iot-hub-csharp-csharp-getstarted.md) (Aan de slag met IoT Hub) voor stapsgewijze instructies. 

## <a name="create-a-logic-app"></a>Een logische app maken

First, create a logic app and add an event grid trigger that monitors the resource group for your virtual machine. 

### <a name="create-a-logic-app-resource"></a>Een logische app maken

1. In the [Azure portal](https://portal.azure.com), select **Create a resource**, then type "logic app" in the search box and select return. Select **Logic App** from the results.

   ![Logische app maken](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. On the next screen, select **Create**. 

1. Geef een naam op voor de logische app die uniek is in uw abonnement en selecteer vervolgens het abonnement, de resourcegroep en de locatie van uw IoT-hub. 

   ![Fields for create logic app](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Selecteer **Maken**.

1. Als de resource is gemaakt, navigeert u naar uw logische app. To do this, select **Resource groups**, then select the resource group you created for this tutorial. Then find the logic app in the list of resources and select it. 

1. In the Logic Apps Designer, page down to see **Templates**. Choose **Blank Logic App** so that you can build your logic app from scratch.

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

1. Selecteer **Nieuwe stap**. This opens a window to **Choose an action**.

1. Zoek naar **e-mail**.

1. Zoek en selecteer de bijbehorende connector op basis van uw e-mailprovider. In deze zelfstudie wordt **Office 365 Outlook** gebruikt. De stappen voor andere e-mailproviders zijn vergelijkbaar. 

   ![Connector voor e-mailprovider selecteren](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. Selecteer de actie **Een e-mail verzenden**. 

1. Meld u aan bij uw e-mailaccount als dat wordt gevraagd. 

1. Stel de e-mailsjabloon samen. 

   * **Aan**: voer het e-mailadres in waarop u de e-mailmeldingen wilt ontvangen. Gebruik voor deze zelfstudie een e-mailaccount dat toegankelijk is voor testdoeleinden. 

   * **Subject**: Fill in the text for the subject. When you click on the Subject text box, you can select dynamic content to include. For example, this tutorial uses `IoT Hub alert: {event Type}`. If you can't see Dynamic content, select the **Add dynamic content** hyperlink -- this toggles it on and off.

   * **Body**: Write the text for your email. Selecteer JSON-eigenschappen in het selectiehulpmiddel om dynamische inhoud op te nemen op basis van gegevens van gebeurtenissen. If you can't see the Dynamic content, select the **Add dynamic content** hyperlink under the **Body** text box. If it doesn't show you the fields you want, click *more* in the Dynamic content screen to include the fields from the previous action.

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

1. Ga in Azure Portal naar uw IoT-hub. You can do this by selecting **Resource groups**, then select the resource group for this tutorial, and then select your IoT hub from the list of resources.

2. Selecteer **Gebeurtenissen**.

   ![Details van gebeurtenisraster weergeven](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Selecteer **Gebeurtenisabonnement**. 

   ![Nieuw gebeurtenisabonnement maken](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Maak het gebeurtenisabonnement met de volgende waarden: 

   * **Event Subscription Details**: Provide a descriptive name and select **Event Grid Schema**.

   * **Event Types**: In the **Filter to Event Types**, uncheck all of the choices except **Device Created**.

       ![subscription event types](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)

   * **Endpoint Details**: Select Endpoint Type as **Web Hook** and select *select an endpoint* and paste the URL that you copied from your logic app and confirm selection.

     ![eindpunt-URL selecteren](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

   When you're done, the pane should look like the following example: 

    ![Voorbeeld van formulier voor gebeurtenisabonnement](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. U kunt het gebeurtenisabonnement nu opslaan en dan meldingen ontvangen voor elk apparaat dat wordt gemaakt in uw IoT-hub. Voor deze zelfstudie gaan we echter de optionele velden gebruiken om te filteren op specifieke apparaten. Select **Filters** at the top of the pane.

6. Select **Add new filter**. Fill in the fields with these values:

   * **Key**: Select `Subject`.

   * **Operator**: Select `String begins with`.

   * **Value**:  Enter `devices/Building1_` to filter for device events in building 1.
  
   Add another filter with these values:

   * **Key**: Select `Subject`.

   * **Operator**: Select `String ends with`.

   * **Value**: Enter `_Temperature` to filter for device events related to temperature.

   The **Filters** tab of your event subscription should now look similar to this image:

   ![Adding filters to event subscription](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

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

   If you added the four examples, your list of IoT devices should look like the following image:

   ![IoT Hub device list](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. Als u een paar apparaten hebt toegevoegd aan uw IoT-hub, controleert u of er e-mail is om te zien welke acties de logische app hebben geactiveerd. 

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

In plaats van Azure Portal te gebruiken, kunt u de stappen voor IoT Hub ook uitvoeren met de Azure CLI. For details, see the Azure CLI pages for [creating an event subscription](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) and [creating an IoT device](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity).

## <a name="clean-up-resources"></a>Resources opschonen

In deze zelfstudie zijn resources gebruikt die kosten voor uw Azure-abonnement met zich meebrengen. When you're finished trying out the tutorial and testing your results, disable or delete resources that you don't want to keep. 

To delete all of the resources created in this tutorial, delete the resource group. 

1. Select **Resource groups**, then select the resource group you created for this tutorial.

2. On the Resource group pane, select **Delete resource group**. You are prompted to enter the resource group name, and then you can delete it. All of the resources contained therein are also removed.

If you don't want to remove all of the resources, you can manage them one by one. 

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
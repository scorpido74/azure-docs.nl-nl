---
title: Bewaking en meldingen op afstand van IoT met Azure Logic Apps | Microsoft Documenten
description: Gebruik Azure Logic Apps voor IoT-temperatuurbewaking op uw IoT-hub en stuur automatisch e-mailmeldingen naar uw postvak voor eventuele afwijkingen die worden gedetecteerd.
author: robinsh
keywords: iot-monitoring, iot-meldingen, iot-temperatuurbewaking
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: 2720f9acfa308294b30f9203ba80e3f9b426e1e9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680720"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>IoT-bewaking en meldingen op afstand met Azure Logic Apps die uw IoT-hub en -postvak met elkaar verbinden

![End-to-end diagram](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) kan u helpen bij het orkestreren van werkstromen voor on-premises en cloudservices, een of meer ondernemingen en in verschillende protocollen. Een logische app begint met een trigger, die vervolgens wordt gevolgd door een of meer acties die kunnen worden gesequenced met behulp van ingebouwde besturingselementen, zoals voorwaarden en iterators. Deze flexibiliteit maakt Logic Apps een ideale IoT-oplossing voor IoT-bewakingsscenario's. De komst van telemetriegegevens van een apparaat op een IoT Hub-eindpunt kan bijvoorbeeld logische app-werkstromen initiëren om de gegevens in een Azure Storage-blob te verwerken, e-mailwaarschuwingen verzenden om te waarschuwen voor gegevensafwijkingen, een technicusbezoek plannen als een apparaat een storing meldt, enzovoort.

## <a name="what-you-learn"></a>Wat u leert

U leert hoe u een logische app maakt die uw IoT-hub en uw postvak verbindt voor temperatuurbewaking en meldingen.

De clientcode die op uw apparaat `temperatureAlert`wordt uitgevoerd, stelt een eigenschap van een toepassing in, op elk telemetriebericht dat naar uw IoT-hub wordt verzonden. Wanneer de clientcode een temperatuur boven de 30 `true`C detecteert, stelt deze eigenschap in op ; anders stelt het de `false`eigenschap in op .

Berichten die bij uw IoT-hub binnenkomen, lijken op het volgende, met `temperatureAlert` de telemetriegegevens in de hoofdtekst en de eigenschap in de toepassingseigenschappen (systeemeigenschappen worden niet weergegeven):

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

Zie [IoT Hub-berichten maken en lezen](iot-hub-devguide-messages-construct.md)voor meer informatie over de berichtindeling van IoT Hub.

In dit onderwerp stelt u routering in op uw IoT-hub om berichten te verzenden waarin de `temperatureAlert` eigenschap zich bevindt `true` naar een eindpunt van een ServiceBus. Vervolgens stelt u een logische app in die wordt geactiveerd op de berichten die bij het eindpunt van de Servicebus binnenkomen en u een e-mailmelding stuurt.

## <a name="what-you-do"></a>Wat je doet

* Maak een naamruimte voor servicebus en voeg er een wachtrij voor servicebus aan toe.
* Voeg een aangepast eindpunt en een routeringsregel toe aan uw IoT-hub om berichten met een temperatuurwaarschuwing naar de wachtrij van de servicebus te leiden.
* Maak, configureer en test een logische app om berichten uit de wachtrij van servicebus te gebruiken en meldingen te verzenden naar een gewenste ontvanger.

## <a name="what-you-need"></a>Wat u nodig hebt

* Voltooi de [Raspberry Pi online simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) tutorial of een van de apparaat tutorials; bijvoorbeeld [Raspberry Pi met node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Deze hebben betrekking op de volgende eisen:

  * Een actief Azure-abonnement.
  * Een Azure IoT-hub onder uw abonnement.
  * Een clienttoepassing die wordt uitgevoerd op uw apparaat en telemetrieberichten verzendt naar uw Azure IoT-hub.

## <a name="create-service-bus-namespace-and-queue"></a>Naamruimte en wachtrij servicebus maken

Maak een Service Bus-naamruimte en -wachtrij. Later in dit onderwerp maakt u een routeringsregel in uw IoT-hub om berichten te sturen die een temperatuurwaarschuwing bevatten naar de wachtrij servicebus, waar ze worden opgehaald door een logische app en deze activeren om een meldingse-mail te verzenden.

### <a name="create-a-service-bus-namespace"></a>Een Service Bus-naamruimte maken

1. Selecteer op de [Azure-portal](https://portal.azure.com/) **+ Een resource** > **Integration** > **Service Bus maken**.

1. Geef in het deelvenster **Naamruimte maken** de volgende gegevens op:

   **Naam**: De naam van de naamruimte van de servicebus. De naamruimte moet uniek zijn in Azure.

   **Prijscategorie:** Selecteer **Basis** in de vervolgkeuzelijst. De basislaag is voldoende voor deze zelfstudie.

   **Resourcegroep:** gebruik dezelfde resourcegroep die uw IoT-hub gebruikt.

   **Locatie:** gebruik dezelfde locatie als uw IoT-hub.

   ![Een naamruimte voor servicebussen maken in de Azure-portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Selecteer **Maken**. Wacht tot de implementatie is voltooid voordat u doorgaat naar de volgende stap.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Een wachtrij voor servicebus toevoegen aan de naamruimte

1. Open de naamruimte servicebus. De eenvoudigste manier om naar de naamruimte van de Servicebus te gaan, is **door Resourcegroepen** te selecteren in het resourcevenster, uw resourcegroep te selecteren en vervolgens de naamruimte servicebus te selecteren in de lijst met bronnen.

1. Selecteer in het deelvenster **Naamruimte van servicebus** de optie **+ Wachtrij**.

1. Voer een naam in voor de wachtrij en selecteer **Maken**. Wanneer de wachtrij is gemaakt, wordt het **wachtrijvenster maken** gesloten.

   ![Een servicebuswachtrij toevoegen aan de Azure-portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Terug in het deelvenster **Naamruimte servicebus** onder **Entiteiten**selecteert u **Wachtrijen**. Open de wachtrij servicebus in de lijst en selecteer **Beleid** > voor gedeelde toegang **+ Toevoegen**.

1. Voer een naam in voor het beleid, controleer **Beheren**en selecteer **Vervolgens Maken**.

   ![Een wachtrijbeleid voor servicebussen toevoegen aan de Azure-portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Een aangepaste eindpunt- en routeringsregel toevoegen aan uw IoT-hub

Voeg een aangepast eindpunt voor de wachtrij servicebus toe aan uw IoT-hub en maak een regel voor het routeren van berichten voor directe berichten die een temperatuurwaarschuwing bevatten naar dat eindpunt, waar ze worden opgehaald door uw logische app. De routeringsregel maakt `temperatureAlert = "true"`gebruik van een routeringsquery om `temperatureAlert` berichten door te sturen op basis van de waarde van de eigenschap van de toepassing die is ingesteld door de clientcode die op het apparaat wordt uitgevoerd. Zie [Opdracht voor berichtroutering op basis van berichteigenschappen](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties)voor meer informatie.

### <a name="add-a-custom-endpoint"></a>Een aangepast eindpunt toevoegen

1. Open je IoT-hub. De eenvoudigste manier om naar de IoT-hub te gaan, is **door Resourcegroepen** te selecteren in het resourcevenster, uw resourcegroep te selecteren en vervolgens uw IoT-hub te selecteren in de lijst met bronnen.

1. Selecteer **onder Berichten**de optie **Berichtroutering**. Selecteer in het deelvenster **Berichtroutering** het tabblad **Aangepaste eindpunten** en selecteer **+ Toevoegen**. Selecteer in de vervolgkeuzelijst de optie **Wachtrij voor servicebussen**.

   ![Een eindpunt toevoegen aan uw IoT-hub in de Azure-portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. Voer in het **eindpuntvan de servicebus** de volgende gegevens in:

   **Eindpuntnaam:** de naam van het eindpunt.

   **Naamruimte servicebus**: selecteer de naamruimte die u hebt gemaakt.

   **Wachtrij voor servicebussen**: selecteer de wachtrij die u hebt gemaakt.

   ![Een eindpunt toevoegen aan uw IoT-hub in de Azure-portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Selecteer **Maken**. Nadat het eindpunt is gemaakt, gaat u verder met de volgende stap.

### <a name="add-a-routing-rule"></a>Een routeringsregel toevoegen

1. Selecteer terug in het **routeringsvenster van het bericht** het tabblad **Routes** en selecteer **+ Toevoegen**.

1. Voer in het deelvenster **Een route toevoegen** de volgende gegevens in:

   **Naam:** de naam van de routeringsregel.

   **Eindpunt:** selecteer het eindpunt dat u hebt gemaakt.

   **Gegevensbron**: Selecteer **Telemetrieberichten van apparaten**.

   **Routeringsquery:** Enter `temperatureAlert = "true"`.

   ![Een routeringsregel toevoegen in de Azure-portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Selecteer **Opslaan**. U het **routeringsvenster voor berichten** sluiten.

## <a name="create-and-configure-a-logic-app"></a>Een logische app maken en configureren

In de vorige sectie stelt u uw IoT-hub in om berichten met een temperatuurwaarschuwing naar uw wachtrij voor servicebus te leiden. U stelt nu een logische app in om de wachtrij voor servicebus te controleren en een e-mailmelding te verzenden wanneer een bericht aan de wachtrij wordt toegevoegd.

### <a name="create-a-logic-app"></a>Een logische app maken

1. Selecteer **Een logica-app voor** > **resourceintegratie** > **maken**.

1. Voer de volgende informatie in:

   **Naam:** De naam van de logische app.

   **Resourcegroep:** gebruik dezelfde resourcegroep die uw IoT-hub gebruikt.

   **Locatie:** gebruik dezelfde locatie als uw IoT-hub.

   ![Een logische app maken in de Azure-portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Selecteer **Maken**.

### <a name="configure-the-logic-app-trigger"></a>De trigger van de logische app configureren

1. Open de logische app. De eenvoudigste manier om naar de logische app te gaan, is door **Resourcegroepen** in het resourcevenster te selecteren, uw resourcegroep te selecteren en vervolgens uw logische app te selecteren in de lijst met bronnen. Wanneer u de logische app selecteert, wordt de Logic Apps Designer geopend.

1. Schuif in de Logic Apps Designer omlaag naar **Sjablonen** en selecteer **Blank Logic App**.

   ![Begin met een lege logische app in de Azure-portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Selecteer het tabblad **Alles** en selecteer **vervolgens Servicebus**.

   ![Selecteer Servicebus om uw logische app te maken in de Azure-portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Selecteer **onder Triggers**de optie Wanneer een of meer berichten in een wachtrij binnenkomen **(automatisch aanvullen).**

   ![De trigger voor uw logische app selecteren in de Azure-portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Maak een servicebusverbinding.
   1. Voer een verbindingsnaam in en selecteer de naamruimte servicebus in de lijst. Het volgende scherm wordt geopend.

      ![Een servicebusverbinding maken voor uw logische app in de Azure-portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Selecteer het servicebusbeleid (RootManageSharedAccessKey). Selecteer vervolgens **Maken**.

      ![Een servicebusverbinding maken voor uw logische app in de Azure-portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Selecteer in het laatste scherm voor **Wachtrijnaam**de wachtrij die u hebt gemaakt in de vervolgkeuzelijst. Voer `175` in voor **Het aantal meldingen van maximaal berichten**.

      ![Het maximale aantal berichten voor de servicebusverbinding in uw logische app opgeven](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Selecteer **Opslaan** in het menu boven aan de Logic Apps Designer om uw wijzigingen op te slaan.

### <a name="configure-the-logic-app-action"></a>De actie logische app configureren

1. Maak een SMTP-serviceverbinding.

   1. Selecteer **Nieuwe stap**. Selecteer **in Een actie kiezen**het tabblad **Alles.**

   1. Typ `smtp` in het zoekvak, selecteer de **SMTP-service** in het zoekresultaat en selecteer **E-mail verzenden**.

      ![Een SMTP-verbinding maken in uw logische app in de Azure-portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Voer de SMTP-gegevens voor uw postvak in en selecteer **Maak .**

      ![SMTP-verbindingsgegevens invoeren in uw logische app in de Azure-portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Download de SMTP-informatie voor [Hotmail/Outlook.com,](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970) [Gmail](https://support.google.com/a/answer/176600?hl=en)en [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Mogelijk moet u TLS/SSL uitschakelen om de verbinding tot stand te brengen. Als dit het geval is en u TLS opnieuw wilt inschakelen nadat de verbinding is gemaakt, raadpleegt u de optionele stap aan het einde van deze sectie.

   1. Selecteer **in** de vervolgkeuzelijst Nieuwe parameter toevoegen in de stap **E-mail verzenden** de optie **Van** **, Naar**, **Onderwerp** en **Hoofdtekst**. Klik of tik ergens op het scherm om het selectievak te sluiten.

      ![E-mailvelden voor SMTP-verbindingen kiezen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Voer uw e-mailadres in `High temperature detected` voor **Van** en **Naar,** en voor **Onderwerp** en **Lichaam.** Als de **dynamische inhoud toevoegen van de apps en connectoren die in dit stroomdialoogvenster worden gebruikt,** wordt geopend, selecteert u **Verbergen** om deze te sluiten. U gebruikt geen dynamische inhoud in deze zelfstudie.

      ![E-mailvelden voor invulsen smtp-verbinding](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Selecteer **Opslaan** om de SMTP-verbinding op te slaan.

1. (Optioneel) Als u TLS moest uitschakelen om een verbinding met uw e-mailprovider tot stand te brengen en deze opnieuw wilt inschakelen, voert u de volgende stappen uit:

   1. Selecteer **API-verbindingen**in het deelvenster **Logic-app** onder **Hulpmiddelen voor ontwikkeling**.

   1. Selecteer in de lijst met API-verbindingen de SMTP-verbinding.

   1. Selecteer in het deelvenster **Smtp API Connection** onder **Algemeen**de optie **API-verbinding bewerken**.

   1. Selecteer in het deelvenster **API-verbinding bewerken** de optie **SSL inschakelen?** **Save**

      ![SMTP-API-verbinding bewerken in uw logische app in de Azure-portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Uw logische app is nu klaar om temperatuurwaarschuwingen uit de wachtrij van de servicebus te verwerken en meldingen naar uw e-mailaccount te verzenden.

## <a name="test-the-logic-app"></a>Test de logische app

1. Start de clienttoepassing op uw apparaat.

1. Als u een fysiek apparaat gebruikt, brengt u een warmtebron voorzichtig in de buurt van de warmtesensor totdat de temperatuur meer dan 30 graden C bedraagt. Als u de online simulator gebruikt, wordt de clientcode willekeurig uitgevoerd telemetrieberichten die meer dan 30 C.

1. U moet beginnen met het ontvangen van e-mailmeldingen die door de logische app worden verzonden.

   > [!NOTE]
   > Uw e-mailserviceprovider moet mogelijk de afzenderidentiteit verifiëren om er zeker van te zijn dat u de e-mail verzendt.

## <a name="next-steps"></a>Volgende stappen

U hebt met succes een logische app gemaakt die uw IoT-hub en uw postvak verbindt voor temperatuurbewaking en meldingen.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

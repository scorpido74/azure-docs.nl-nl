---
title: 'Zelf studie: gebeurtenissen vastleggen vanuit een Space-Azure Digital Apparaatdubbels | Microsoft Docs'
description: Informatie over hoe u meldingen ontvangt uit uw ruimten door Azure Digital Twins te integreren met logische apps met behulp van de stappen in deze zelfstudie.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 09/23/2019
ms.openlocfilehash: 00efae0b87de90d2abb1d488afa6b51b1b188b30
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74009285"
---
# <a name="tutorial-receive-notifications-from-your-azure-digital-twins-spaces-by-using-logic-apps"></a>Zelfstudie: Meldingen ontvangen uit uw Azure Digital Twins-ruimten met behulp van Logic Apps

Nadat u uw Azure Digital Twins-exemplaar hebt geïmplementeerd, uw opslagruimten hebt ingericht en aangepaste functies hebt geïmplementeerd voor het bewaken van specifieke voorwaarden, kunt u uw gebouwbeheerder via e-mail waarschuwen wanneer de bewaakte voorwaarden optreden.

In [de eerste zelfstudie](tutorial-facilities-setup.md) hebt u de ruimtelijke grafiek van een denkbeeldige gebouw geconfigureerd. Een kamer in het gebouw bevat sensoren voor beweging, kooldioxide en temperatuur. In [de tweede zelfstudie](tutorial-facilities-udf.md) hebt u uw grafiek en een door de gebruiker gedefinieerde functie ingericht om deze sensorwaarden te bewaken en meldingen te activeren wanneer de ruimte leeg is en de waarden voor temperatuur en koolstofdioxide in orde zijn. 

In deze zelfstudie leert hoe u deze meldingen kunt integreren met Azure Logic Apps om e-mails te verzenden wanneer een dergelijke ruimte beschikbaar is. Een gebouwbeheerder kan deze informatie gebruiken om werknemers te helpen de meest productieve vergaderruimte te boeken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gebeurtenissen integreren met Azure Event Grid.
> * Gebeurtenissen melden met Logic Apps.

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgegaan dat u de Azure Digital Twins-installatie hebt [geconfigureerd](tutorial-facilities-setup.md) en [ingericht](tutorial-facilities-udf.md). Zorg er, vóórdat u doorgaat, voor dat u beschikt over:

- Een [Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Een actief exemplaar van Digital Twins.
- De gedownloade en uitgepakte [Digital Twins C#-voorbeelden](https://github.com/Azure-Samples/digital-twins-samples-csharp) op een werkcomputer.
- [.NET Core SDK-versie 2.1.403 of hoger](https://www.microsoft.com/net/download) op een ontwikkelcomputer om het voorbeeld uit te voeren. Voer `dotnet --version` uit om te controleren of de juiste versie is geïnstalleerd.
- Een Office 365-account voor het verzenden van melding via e-mail.

> [!TIP]
> Gebruik een unieke Digital Apparaatdubbels-exemplaar naam als u een nieuw exemplaar inricht.

## <a name="integrate-events-with-event-grid"></a>Gebeurtenissen integreren met Event Grid

In deze sectie stelt u een [Event Grid](../event-grid/overview.md) in voor het verzamelen van gebeurtenissen uit uw Azure Digital Twins-instantie en stuurt u deze door naar een [gebeurtenis-handler](../event-grid/event-handlers.md) zoals Azure Logic Apps.

### <a name="create-an-event-grid-topic"></a>Een Event Grid-onderwerp maken

[Event Grid-onderwerpen](../event-grid/concepts.md#topics) bieden een interface voor het routeren van de gebeurtenissen die worden gegenereerd door de functie die door de gebruiker is gedefinieerd. 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

1. Selecteer **Een resource maken** in het linkerdeelvenster. 

1. Zoek en selecteer **Event Grid-onderwerp**. Selecteer **Maken**.

1. Voer een **Naam** in voor uw Event Grid-onderwerp en kies het **Abonnement**. Selecteer de **Resourcegroep** die u hebt gebruikt of gemaakt voor uw Digital Twins-instantie en selecteer de **Locatie**. Selecteer **Maken**. 

    [een event grid-onderwerp ![maken](./media/tutorial-facilities-events/create-event-grid-topic.png)](./media/tutorial-facilities-events/create-event-grid-topic.png#lightbox)

1. Blader naar het Event Grid-onderwerp in de resourcegroep, selecteer **Overzicht** en kopieer de waarde voor **Eindpunt onderwerp** naar een tijdelijk bestand. U hebt deze URL nodig in de volgende sectie. 

1. Selecteer **Toegangssleutels** en kopieer **UW_SLEUTEL_1** en **UW_SLEUTEL_ 2** naar een tijdelijk bestand. U hebt deze waarden nodig om het eindpunt in de volgende sectie te maken.

    [Event Grid sleutels ![](./media/tutorial-facilities-events/event-grid-keys.png)](./media/tutorial-facilities-events/event-grid-keys.png#lightbox)

### <a name="create-an-endpoint-for-the-event-grid-topic"></a>Een eindpunt voor het Event Grid-onderwerp maken

1. Zorg dat u in het opdrachtvenster de map **occupancy-quickstart\src** van het Digital Twins-voorbeeld hebt geopend.

1. Open het bestand **actions\createEndpoints.yaml** in de Visual Studio Code-editor. Zorg ervoor dat het de volgende inhoud bevat:

    ```yaml
    - type: EventGrid
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: <Primary connection string for your Event Grid>
      secondaryConnectionString: <Secondary connection string for your Event Grid>
      path: <Event Grid Topic Name without https:// and /api/events, e.g. eventgridname.region.eventgrid.azure.net>
    ```

1. Vervang de tijdelijke aanduiding `<Primary connection string for your Event Grid>` door de waarde van **YOUR_KEY_1**.

1. Vervang de tijdelijke aanduiding `<Secondary connection string for your Event Grid>` door de waarde van **YOUR_KEY_2**.

1. Vervang de tijdelijke aanduiding voor het **pad** door het pad naar het event grid-onderwerp. Haal dit pad op door **https://** en de daarop volgende resourcepaden uit de URL van het **Eindpunt onderwerp** te verwijderen. Het moet er uitzien zoals deze indeling: *yourEventGridName.yourLocation.eventgrid.azure.net*.

    > [!IMPORTANT]
    > Voer alle waarden in zonder aanhalingstekens. Zorg dat de dubbele punten in het YAML-bestand worden gevolgd door minstens één spatie. U kunt de inhoud van het YAML-bestand ook valideren met behulp van een YAML-onlinevalidatie, zoals met [dit hulpprogramma](https://onlineyamltools.com/validate-yaml).

1. Sla het bestand op en sluit het. Voer de volgende opdracht uit in het opdrachtvenster en meld u aan wanneer u hierom wordt gevraagd. 

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Met deze opdracht maakt u het eindpunt voor de Event Grid. 

   [![eind punten voor Event Grid](./media/tutorial-facilities-events/dotnet-create-endpoints.png)](./media/tutorial-facilities-events/dotnet-create-endpoints.png#lightbox)

## <a name="notify-events-with-logic-apps"></a>Gebeurtenissen melden met Logic Apps

Met de [Azure Logic Apps](../logic-apps/logic-apps-overview.md)-service kunt u geautomatiseerde taken maken voor gebeurtenissen die zijn ontvangen van andere services. In deze sectie stelt u Azure Logic Apps in om e-mailmeldingen te maken voor gebeurtenissen die zijn doorgestuurd vanuit uw ruimtesensoren, met behulp van een [Event Grid-onderwerp](../event-grid/overview.md).

1. Selecteer in het linkerdeelvenster van de [Azure-portal](https://portal.azure.com) de optie **Een resource maken**.

1. Zoek en selecteer een nieuwe resource voor de **logische app**. Selecteer **Maken**.

1. Voer een **Naam** in voor uw Logic Apps-resource en selecteer uw **Abonnement**, **Resourcegroep** en **Locatie**. Selecteer **Maken**.

    [![een Logic Apps resource maken](./media/tutorial-facilities-events/create-logic-app.png)](./media/tutorial-facilities-events/create-logic-app.png#lightbox)

1. Open de resource voor Logic Apps wanneer deze is geïmplementeerd en open vervolgens het deelvenster **Ontwerper van logische app**. 

1. Selecteer de trigger **Wanneer een event grid bron gebeurtenis plaatsvindt** . Meld u aan bij uw tenant met uw Azure-account wanneer hierom wordt gevraagd. Selecteer **toegang toestaan** voor uw event grid resource als u hierom wordt gevraagd. Selecteer **Doorgaan**.

1. In het venster **Wanneer een resourcegebeurtenis optreedt (Preview)** : 
   
   a. Selecteer het **abonnement** waarmee u het Event Grid-onderwerp hebt gemaakt.

   b. Selecteer **Microsoft.EventGrid.Topics** als **Resourcetype**.

   c. Selecteer uw Event Grid-resource in de vervolgkeuzelijst voor de **Resourcenaam**.

   [deel venster ![Logic app Designer](./media/tutorial-facilities-events/logic-app-resource-event.png)](./media/tutorial-facilities-events/logic-app-resource-event.png#lightbox)

1. Selecteer de knop **Nieuwe stap**.

1. In het venster **Kies een actie**:

   a. Zoekt u de woordgroep **JSON parseren** en selecteert u de actie **JSON parseren**.

   b. Selecteer in het veld **Inhoud** de optie **Hoofdtekst** in de lijst **Dynamische inhoud**.

   c. Selecteer **Voorbeeldnettolading om een schema te genereren**. Plak de volgende JSON-nettolading en selecteer **Gereed**.

    ```JSON
    {
    "id": "32162f00-a8f1-4d37-aee2-9312aabba0fd",
    "subject": "UdfCustom",
    "data": {
      "TopologyObjectId": "20efd3a8-34cb-4d96-a502-e02bffdabb14",
      "ResourceType": "Space",
      "Payload": "\"Air quality is poor.\"",
      "CorrelationId": "32162f00-a8f1-4d37-aee2-9312aabba0fd"
    },
    "eventType": "UdfCustom",
    "eventTime": "0001-01-01T00:00:00Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/a382ee71-b48e-4382-b6be-eec7540cf271/resourceGroups/HOL/providers/Microsoft.EventGrid/topics/DigitalTwinEventGrid"
    }
    ```

    Deze nettolading heeft fictieve waarden. Logic Apps maakt gebruik van dit voorbeeld van nettolading voor het genereren van een *Schema*.

    [![Logic Apps JSON-venster voor Event Grid parseren](./media/tutorial-facilities-events/logic-app-parse-json.png)](./media/tutorial-facilities-events/logic-app-parse-json.png#lightbox)

1. Selecteer de knop **Nieuwe stap**.

1. In het venster **Kies een actie**:

   a. Selecteer **Besturing > Voorwaarde** of zoek **Voorwaarde** in de lijst **Acties**. 

   b. Selecteer in het eerste tekstvak van **Een waarde kiezen** de optie **Type gebeurtenis** in de lijst **Dynamische inhoud** voor het venster **JSON parseren**.

   c. In het tweede tekstvak **Kies een waarde** voert u `UdfCustom` in.

   [Geselecteerde voor waarden ![](./media/tutorial-facilities-events/logic-app-condition.png)](./media/tutorial-facilities-events/logic-app-condition.png#lightbox)

1. In het venster **Indien waar**:

   a. Selecteer **Een actie toevoegen** en selecteer **Office 365 Outlook**.

   b. Selecteer in de lijst **Acties** de optie **Een e-mail verzenden**. Selecteer **Aanmelden** en gebruik de referenties van uw e-mailaccount. Selecteer **toegang toestaan** als u hierom wordt gevraagd.

   c. In het vak **Aan** voert u uw e-mail-ID in om meldingen te ontvangen. Voer bij **Onderwerp** de tekst **Digital Twins-melding voor slechte luchtkwaliteit in ruimte**. Selecteer vervolgens **TopologyObjectId** in de lijst **Dynamische inhoud** voor **JSON parseren**.

   d. Voer onder **Hoofdtekst** in hetzelfde venster een soortgelijke tekst als deze in: **Slechte luchtkwaliteit gedetecteerd in een ruimte en temperatuur moet worden aangepast**. U kunt dit gerust uitbreiden met behulp van elementen uit de lijst **Dynamische inhoud**.

   [![Logic Apps ' een e-mail verzenden '](./media/tutorial-facilities-events/logic-app-send-email.png)](./media/tutorial-facilities-events/logic-app-send-email.png#lightbox)

1. Selecteer de knop **Opslaan** boven aan het deelvenster **Ontwerper van logische app**.

1. Zorg ervoor dat u sensorgegevens simuleert door te navigeren naar de map **device-connectivity** van het Digital Twin-voorbeeld in een opdrachtvenster en `dotnet run` uit te voeren.

Binnen een paar minuten moet u e-mailmeldingen gaan ontvangen van deze resource voor Logic Apps. 

   [e-mail melding ![](./media/tutorial-facilities-events/logic-app-notification.png)](./media/tutorial-facilities-events/logic-app-notification.png#lightbox)

Als u wilt stoppen met het ontvangen van deze e-mailberichten, gaat u naar uw resource voor Logic Apps in de portal en selecteert u het deelvenster **Overzicht**. Selecteer **Uitschakelen**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u Azure Digital Twins niet verder wilt verkennen, kunt u de resources die in deze zelfstudie zijn gemaakt, gerust verwijderen:

1. Klik in het linkermenu in de [Azure-portal](https://portal.azure.com) op **Alle resources**, selecteer de Digital Twins-resourcegroep en selecteer **Verwijderen**.

    > [!TIP]
    > Als u problemen hebt bij het verwijderen van uw Digital Twins-exemplaar, is er een service-update met de oplossing hiervoor beschikbaar. Probeer opnieuw of u het exemplaar kunt verwijderen.

2. Verwijder zo nodig de voorbeeldtoepassingen van uw werkcomputer.

## <a name="next-steps"></a>Volgende stappen

Ga verder met de volgende zelfstudie voor meer informatie over hoe u uw sensorgegevens kunt visualiseren, trends kunt analyseren en afwijkingen kunt vinden:

> [!div class="nextstepaction"]
> [Zelfstudie: Gebeurtenissen uit Azure Digital Twins-ruimten visualiseren en analyseren met Time Series Insights](tutorial-facilities-analyze.md)

U kunt ook meer leren over grafieken voor ruimtelijke intelligentie en objectmodellen in Azure Digital Twins:

> [!div class="nextstepaction"]
> [Begrip van objectmodellen en grafieken voor ruimtelijke intelligentie van Digital Twins](concepts-objectmodel-spatialgraph.md)

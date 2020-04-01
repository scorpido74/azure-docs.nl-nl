---
title: 'Zelfstudie: Gebeurtenissen analyseren in Time Series Insights - Azure Digital Twins| Microsoft Documenten'
description: Leer hoe u met behulp van de stappen in deze zelfstudie gebeurtenissen uit Azure Digital Twins-ruimten kunt visualiseren en analyseren met Azure Time Series Insights.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 38bd1755ed87050cf8b91a0a82f6e5f1d2af9db5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75933875"
---
# <a name="tutorial-visualize-and-analyze-events-from-azure-digital-twins-by-using-time-series-insights"></a>Zelfstudie: Gebeurtenissen van Azure Digital Twins visualiseren en analyseren met behulp van Time Series Insights

Nadat u het Azure Digital Twins-exemplaar hebt geïmplementeerd, de ruimten hebt ingericht, en een aangepaste functie hebt geïmplementeerd om specifieke voorwaarden te bewaken, kunt u de gebeurtenissen en gegevens visualiseren die afkomstig zijn uit deze ruimten. Op deze manier kunt u eventuele trends en afwijkingen detecteren.

In [de eerste zelfstudie](tutorial-facilities-setup.md) hebt u de ruimtelijke grafiek van een denkbeeldig gebouw geconfigureerd, met hierin een ruimte met sensoren voor beweging, koolstofdioxide en temperatuur. in de [tweede zelfstudie](tutorial-facilities-udf.md) hebt u deze grafiek ingericht, samen met een door de gebruiker gedefinieerde functie. De functie controleert deze sensorwaarden en activeert meldingen voor de juiste voorwaarden. Dat wil zeggen dat de ruimte leeg is en de temperatuur en het koolstofdioxidegehalte normaal zijn.

In deze zelfstudie ziet u hoe u de meldingen en gegevens afkomstig van de Azure Digital Twins-installatie kunt integreren met behulp van Azure Time Series Insights. Vervolgens kunt u de sensorwaarden in de loop van de tijd visualiseren. U kunt trends zoeken, bijvoorbeeld welke ruimte het meest wordt gebruikt en wat de drukste tijdstippen op een dag zijn. U kunt ook afwijkingen detecteren, zoals welke ruimten bedompt of heet aanvoelen, of wanneer voor een gebied in het gebouw consistent hoge temperatuurwaarden worden verzonden, wat duidt op een defecte airconditioning.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gegevens streamen met behulp van Azure Event Hubs.
> * Analyseren met Time Series Insights.

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgegaan dat u de Azure Digital Twins-installatie hebt [geconfigureerd](tutorial-facilities-setup.md) en [ingericht](tutorial-facilities-udf.md). Zorg er, vóórdat u doorgaat, voor dat u beschikt over:

- Een [Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Een actief exemplaar van Digital Twins.
- De [Digital Twins C# samples](https://github.com/Azure-Samples/digital-twins-samples-csharp) gedownload en geëxtraheerd op je werkmachine.
- [.NET Core SDK-versie 2.1.403 of hoger](https://www.microsoft.com/net/download) op een ontwikkelcomputer om het voorbeeld uit te voeren. Voer `dotnet --version` uit om te controleren of de juiste versie is geïnstalleerd.

> [!TIP]
> Gebruik een unieke digital twins-instantienaam als u een nieuw exemplaar indient.

## <a name="stream-data-by-using-event-hubs"></a>Gegevens streamen met Event Hubs

Gebruik de service [Event Hubs](../event-hubs/event-hubs-about.md) om een pijplijn te maken om gegevens te streamen. In deze sectie ziet u hoe u een Event Hub kunt maken als de connector tussen een Azure Digital Twins- en een Time Series Insights-exemplaar.

### <a name="create-an-event-hub"></a>Een Event Hub maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Een resource maken** in het linkerdeelvenster.

1. Zoek en selecteer **Event Hubs**. Selecteer **Maken**.

    [![Naamruimte voor gebeurtenishubs maken](./media/tutorial-facilities-analyze/tutorial-create-event-hubs.png)](./media/tutorial-facilities-analyze/tutorial-create-event-hubs.png#lightbox)

1. Voer een **naam** in voor de naamruimte van de Event Hub. Kies **Standard** voor **Prijscategorie**, uw **Abonnement**, de **Resourcegroep** die u hebt gebruikt voor het Digital Twins-exemplaar, en de **Locatie**. Selecteer **Maken**.

1. Selecteer in de implementatie van de naamruimte van Gebeurtenishubs het deelvenster **Overzicht** en selecteer **Ga naar resource**.

    [![Event Hubs-naamruimte na implementatie](./media/tutorial-facilities-analyze/tutorial-event-hub-ns.png)](./media/tutorial-facilities-analyze/tutorial-event-hub-ns.png#lightbox)

1. Selecteer bovenaan in het deelvenster **Overzicht** van de Event Hubs-naamruimte de knop **Event Hub**.
    [![Knop Gebeurtenishub](./media/tutorial-facilities-analyze/tutorial-create-event-hub.png)](./media/tutorial-facilities-analyze/tutorial-create-event-hub.png#lightbox)

1. Voer een **naam** in voor de Event Hub en selecteer **Maken**.

   Nadat de Event Hub is geïmplementeerd, wordt deze met de status **Actief** weergegeven in het deelvenster **Event Hubs** van de Event Hubs-naamruimte. Selecteer deze Event Hub om het bijbehorende deelvenster **Overzicht** te openen.

1. Selecteer bovenaan de knop **Consumentengroep** en voer een naam in voor de consumentengroep, bijvoorbeeld **tsievents**. Selecteer **Maken**.

    [![Event Hub-consumentengroep](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)](./media/tutorial-facilities-analyze/event-hub-consumer-group.png#lightbox)

   Nadat de consumenten groep is gemaakt, wordt deze weergegeven in de lijst onderaan het deelvenster **Overzicht** van de Event Hub.

1. Open het deelvenster **Beleid voor gedeelde toegang** voor de Event Hub en selecteer de knop **Toevoegen**. Voer **ManageSend** in als de naam van het beleid, controleer of alle selectievakjes zijn ingeschakeld en selecteer **Maken**.

    [![Event Hub-verbindingsreeksen](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)](./media/tutorial-facilities-analyze/event-hub-connection-strings.png#lightbox)

    > [!TIP]
    > Controleer of u een SAS-beleid maakt voor uw gebeurtenishub-instantie in plaats van uw naamruimte.

1. Open het beleid **ManageSend** dat u hebt gemaakt en kopieer de waarden voor **verbindingstekenreeks— primaire toets** en **verbindingstekenreeks— secundaire toets** naar een tijdelijk bestand. U hebt deze waarden in de volgende sectie nodig om een eindpunt te maken voor de Event Hub.

### <a name="create-an-endpoint-for-the-event-hub"></a>Eindpunt maken voor de Event Hub

1. Zorg dat u in het opdrachtvenster de map **occupancy-quickstart\src** van het Azure Digital Twins-voorbeeld hebt geopend.

1. Open het bestand **actions\createEndpoints.yaml** in de editor. Vervang de inhoud door het volgende:

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    ```

1. Vervang de `Primary_connection_string_for_your_event_hub` tijdelijke aanduidingen door de waarde van **verbindingstekenreeks— primaire sleutel** voor de gebeurtenishub. Zorg ervoor dat de indeling van deze verbindingsreeks er als volgt uitziet:

   ```ConnectionString
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. Vervang de `Secondary_connection_string_for_your_event_hub` tijdelijke aanduidingen door de waarde van **verbindingstekenreeks: secundaire toets** voor de gebeurtenishub. Zorg ervoor dat de indeling van deze verbindingsreeks er als volgt uitziet: 

   ```ConnectionString
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. Vervang de tijdelijke aanduidingen `Name_of_your_Event_Hub` door de naam van uw Event Hub.

    > [!IMPORTANT]
    > Voer alle waarden in zonder aanhalingstekens. Zorg dat de dubbele punten in het YAML-bestand worden gevolgd door minstens één spatie. U kunt de inhoud van het YAML-bestand ook valideren met behulp van een YAML-onlinevalidatie, zoals [dit hulpprogramma](https://onlineyamltools.com/validate-yaml).

1. Sla het bestand op en sluit het. Voer de volgende opdracht uit in het opdrachtvenster en meld u aan met uw Azure-account wanneer u hierom wordt gevraagd.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   Hiermee worden twee eindpunten gemaakt voor de Event Hub.

   [![Eindpunten voor Event Hubs](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png#lightbox)

## <a name="analyze-with-time-series-insights"></a>Analyseren met Time Series Insights

1. Selecteer in het linkerdeelvenster van de [Azure-portal](https://portal.azure.com) de optie **Een resource maken**. 

1. Zoek naar en selecteer een **GA-bron (Time Series Insights** General Availability). Selecteer **Maken**.

1. Voer een **naam** in voor het Time Series Insights-exemplaar en selecteer vervolgens uw **abonnement**. Selecteer de **Resourcegroep** die u hebt gebruikt voor het Digital Twins-exemplaar, en uw **Locatie**. Selecteer **Volgende: gebeurtenisbron** of het tabblad **Gebeurtenisbron.**

    [![Selecties voor het maken van een Time Series Insights-exemplaar](./media/tutorial-facilities-analyze/tutorial-create-tsi-environment.png)](./media/tutorial-facilities-analyze/tutorial-create-tsi-environment.png#lightbox)

1. Voer op het tabblad **Gebeurtenisbron** een **naam**in, selecteer **Gebeurtenishub** als **brontype**en controleer of de andere waarden correct zijn geselecteerd om te verwijzen naar de gebeurtenishub die u hebt gemaakt. Selecteer De naam van **het toegangsbeleid voor** **Beheren** voor gebeurtenishub en selecteer vervolgens de consumentengroep die u in de vorige sectie voor **de consumentengroep Van Event Hub**hebt gemaakt. Selecteer **Controleren + maken**.

    [![Selecties voor het maken van een gebeurtenisbron](./media/tutorial-facilities-analyze/tutorial-tsi-event-source.png)](./media/tutorial-facilities-analyze/tutorial-tsi-event-source.png#lightbox)

1. Controleer in het deelvenster **Controleren + Maken** de ingevoerde gegevens en selecteer **Maken**.

1. Selecteer in het implementatievenster de resource Time Series Insights die u hebt gemaakt. Hiermee wordt het **overzichtsvenster** voor uw Time Series Insights-omgeving geopend.

1. Selecteer bovenaan de knop **Ga naar omgeving.** Als u een waarschuwing voor gegevenstoegang te zien krijgt, opent u het deelvenster **Beleid voor gegevenstoegang** voor het Time Series Insights-exemplaar en selecteert u **Toevoegen**. Vervolgens selecteert u de rol **Inzender** en selecteert u de juiste gebruiker.

1. Met de knop **Ga naar omgeving** wordt de [Time Series Insights-verkenner](../time-series-insights/time-series-insights-explorer.md) geopend. Als hier geen gebeurtenissen worden weergegeven, simuleert u apparaatgebeurtenissen door naar het project **device-connectivity** van het Digital Twins-voorbeeld te gaan en `dotnet run` uit te voeren.

1. Nadat een aantal gesimuleerde gebeurtenissen is gegenereerd, gaar u terug naar de Time Series Insights-verkenner en selecteert u bovenaan de knop Vernieuwen. Uw analytische grafieken die worden gemaakt voor uw gesimuleerde sensorgegevens worden weergegeven. 

    [![Grafiek met de Time Series Insights-verkenner](./media/tutorial-facilities-analyze/tsi-explorer-with-adt-telemetry.png)](./media/tutorial-facilities-analyze/tsi-explorer-with-adt-telemetry.png#lightbox)

1. In de Time Series Insights-verkenner kunt u vervolgens grafieken en heatmaps genereren voor verschillende gebeurtenissen en gegevens van de ruimten, sensoren en andere resources. Gebruik de vervolgkeuzelijsten **MEASURE** en **SPLIT BY** aan de linkerkant om uw eigen visualisaties te maken. 

   Selecteer bijvoorbeeld **Gebeurtenissen** voor **MEASURE** en **DigitalTwins-SensorHardwareId** voor **SPLIT BY** om een heatmap te genereren voor elke sensor. De heatmap ziet er ongeveer uit als op de volgende afbeelding:

   [![Heatmap in de Time Series Insights-verkenner](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png#lightbox)

## <a name="clean-up-resources"></a>Resources opschonen

Als u Azure Digital Twins niet verder wilt verkennen, kunt u de resources die in deze zelfstudie zijn gemaakt, gerust verwijderen:

1. Klik in het linkermenu in de [Azure-portal](https://portal.azure.com) op **Alle resources**, selecteer de Digital Twins-resourcegroep en selecteer vervolgens **Verwijderen**.

    > [!TIP]
    > Als u problemen hebt bij het verwijderen van uw Digital Twins-exemplaar, is er een service-update met de oplossing hiervoor beschikbaar. Probeer opnieuw of u het exemplaar kunt verwijderen.

2. Verwijder zo nodig de voorbeeldtoepassingen van uw werkcomputer.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over grafieken voor ruimtelijke intelligentie en objectmodellen in Azure Digital Twins.

> [!div class="nextstepaction"]
> [Begrip van objectmodellen en grafieken voor ruimtelijke intelligentie van Digital Twins](concepts-objectmodel-spatialgraph.md)

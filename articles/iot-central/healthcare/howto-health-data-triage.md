---
title: Een triagedashboard maken met gezondheidsgegevens in Azure IoT Central | Microsoft Docs
description: Meer informatie over het bouwen van een triagedashboard met gezondheidsgegevens met behulp van Azure IoT Central-toepassingssjablonen.
author: philmea
ms.author: philmea
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: ed06aef4d494fbdce5a07c5bc50bad9737ba5433
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86497043"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Zelfstudie: Een Power BI-providerdashboard bouwen



Wanneer u uw bewakingsoplossing voor continue patiëntbewaking bouwt, kunt u ook een dashboard maken voor een zorgteam in een ziekenhuis om patiëntengegevens te visualiseren. In deze zelfstudie leert u hoe u een realtime Power BI-streamingdashboard kunt maken op basis van uw IoT Central-toepassingssjabloon voor continue patiëntbewaking. Als voor uw use-case geen toegang tot realtime gegevens nodig is, kunt u het [IoT Central Power BI-dashboard](../core/howto-connect-powerbi.md) gebruiken, dat een vereenvoudigd implementatieproces kent. 

>[!div class="mx-imgBorder"]
>![Dashboard-GIF](media/dashboard-gif-3.gif)

De basisarchitectuur volgt deze structuur:

>[!div class="mx-imgBorder"] 
>![Triagedashboard van provider](media/dashboard-architecture.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gegevens exporteren van Azure IoT Central naar Azure Event Hubs
> * Een Power BI-streaminggegevensset instellen
> * Uw logische app verbinden met Azure Event Hubs
> * Gegevens streamen naar Power BI vanuit uw logische app
> * Een realtime dashboard bouwen voor vitale gegevens van patiënten

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een Azure IoT Central-toepassingssjabloon voor continue patiëntbewaking. Als u nog geen sjabloon hebt, volgt u de stappen om een [Toepassingssjabloon te implementeren](overview-iot-central-healthcare.md).

* Een Azure [Event Hubs-naamruimte en een Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

* De logische app die u wilt gebruiken voor toegang tot uw Event Hub. Als u uw logische app wilt starten met een Azure Event Hubs-trigger, hebt u een [lege logische app](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow) nodig.

* Een Power BI-serviceaccount. Als u nog geen account hebt, kunt u [een gratis proefaccount maken voor Power BI-service](https://app.powerbi.com/). Als u Power BI nog niet eerder hebt gebruikt, kan het handig zijn om [Aan de slag met Power BI](https://docs.microsoft.com/power-bi/service-get-started) door te lezen.

## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Een continue gegevensexport naar Azure Event Hubs instellen
U moet eerst een continue gegevensexport instellen vanuit uw Azure IoT Central-appsjabloon naar Azure Event Hub in uw abonnement. U kunt dit doen door de stappen in deze Azure IoT Central-zelfstudie te volgen voor het [Exporteren naar Event Hubs](https://docs.microsoft.com/azure/iot-central/core/howto-export-data). Voor de doeleinden van deze zelfstudie hoeft u alleen voor de telemetrie te exporteren.

## <a name="create-a-power-bi-streaming-dataset"></a>Een Power BI-streaminggegevensset maken

1. Meld u aan bij uw Power BI-account.

2. Maak in de gewenste werkruimte een nieuwe streaminggegevensset door de knop **+ Maken** te selecteren in de rechterbovenhoek van de werkbalk. U moet een afzonderlijke gegevensset maken voor elke patiënt die u op uw dashboard wilt hebben.

    >[!div class="mx-imgBorder"] 
    >![Streaminggegevensset maken](media/create-streaming-dataset.png)

3. Kies **API** voor de bron van de gegevensset.

4. Voer een **naam** in (bijvoorbeeld de naam van een patiënt) voor uw gegevensset en vul vervolgens de waarden in vanuit de stroom. U kunt hieronder een voorbeeld zien op basis van waarden die afkomstig zijn van de gesimuleerde apparaten in de toepassingssjabloon voor continue patiëntbewaking. In dit voorbeeld worden twee patiënten gebruikt:

    * Enver Jobse, die gegevens heeft van de Smart Knee Brace
    * Elsje Quint, die gegevens heeft van de Smart Vitals Patch

    >[!div class="mx-imgBorder"] 
    >![Gegevenssetwaarden invoeren](media/enter-dataset-values.png)

Als u meer wilt weten over het streamen van gegevenssets in Power BI, kunt u dit document lezen over [realtime streaming in Power BI](https://docs.microsoft.com/power-bi/service-real-time-streaming).

## <a name="connect-your-logic-app-to-azure-event-hubs"></a>Uw logische app verbinden met Azure Event Hubs
Als u uw logische app wilt verbinden met Azure Event Hubs, kunt u de instructies volgen die in dit document worden beschreven over [het verzenden van gebeurtenissen met Azure Event Hubs en Azure Logic Apps](https://docs.microsoft.com/azure/connectors/connectors-create-api-azure-event-hubs#add-event-hubs-action). Hier volgen enkele aanbevolen parameters:

|Parameter|Waarde|
|---|---|
|Inhoudstype|application/json|
|Interval|3|
|Frequency|Seconde|

Aan het einde van deze stap moet de ontwerpfunctie voor logische apps er als volgt uitzien:

>[!div class="mx-imgBorder"] 
>![Logic Apps maakt verbinding met Event Hubs](media/eh-logic-app.png)

## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Gegevens streamen naar Power BI vanuit uw logische app
De volgende stap bestaat uit het parseren van de gegevens die afkomstig zijn van uw Event Hub om deze te streamen naar de Power BI-gegevenssets die u eerder hebt gemaakt.

1. Voordat u dit kunt doen, moet u inzicht hebben in de JSON-nettolading die vanaf uw apparaat naar uw Event Hub wordt verzonden. U kunt dit doen door naar dit [voorbeeldschema](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#telemetry) te kijken en het te wijzigen, zodat het overeenkomt met uw schema. U kunt ook [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) gebruiken om de berichten te onderzoeken. Als u de toepassingen voor continue patiëntbewaking gebruikt, zien uw berichten er als volgt uit:

**Telemetrie van Smart Vitals Patch**

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```

**Telemetrie van Smart Knee Brace**

```json
{
  "Acceleration": {
    "x": 72.73510947763711,
    "y": 72.73510947763711,
    "z": 72.73510947763711
  },
  "RangeOfMotion": 123,
  "KneeBend": 3
}
```

**Eigenschappen**

```json
{
  "iothub-connection-device-id": "1qsi9p8t5l2",
  "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",  \"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
  "iothub-connection-auth-generation-id": "637063718586331040",
  "iothub-enqueuedtime": 1571681440990,
  "iothub-message-source": "Telemetry",
  "iothub-interface-name": "Patient_health_data_3bk",
  "x-opt-sequence-number": 7,
  "x-opt-offset": "3672",
  "x-opt-enqueued-time": 1571681441317
}
```

2. Nu u de JSON-nettoladingen hebt geïnspecteerd, gaat u terug naar de ontwerpfunctie voor logische apps en selecteert u **+ Nieuwe stap**. Zoek en voeg **Variabele initialiseren** als volgende stap toe en voer de volgende parameters in:

    |Parameter|Waarde|
    |---|---|
    |Naam|Interfacenaam|
    |Type|Tekenreeks|

    Druk op **Opslaan**. 

3. Voeg een andere variabele met de naam **Hoofdtekst** toe met als type **Tekenreeks**. Deze acties worden toegevoegd aan de logische app:

    >[!div class="mx-imgBorder"]
    >![Variabelen initialiseren](media/initialize-string-variables.png)
    
4. Selecteer **+ Nieuwe stap** en voeg de actie **JSON parseren** toe. Wijzig de naam hiervan in **Eigenschappen parseren**. Kies voor de inhoud **eigenschappen** afkomstig van de Event Hub. Selecteer onderaan de optie **Voorbeeld van nettolading gebruiken om schema te genereren** en plak het voorbeeld van de nettolading vanuit de sectie Eigenschappen hierboven.

5. Kies vervolgens de actie **Variabele instellen** en werk de variabele **Interfacenaam** bij met **iothub-interface-name** van de geparseerde JSON-eigenschappen.

6. Voeg het besturingselement **Splitsen** toe als de volgende actie en kies de variabele **Interfacenaam** als parameter voor Aan. U gebruikt deze parameter om de gegevens naar de juiste gegevensset te leiden.

7. Zoek in uw Azure IoT Central-toepassing de interfacenaam voor de gezondheidsgegevens van de Smart Vitals Patch en van de Smart Knee Brace in de weergave **Apparaatsjablonen**. Maak voor elke interfacenaam twee verschillende cases voor het besturingselement **Schakelen** en wijzig de naam van het besturingselement dienovereenkomstig. U kunt de standaardcase zo instellen dat het besturingselement **Beëindigen** wordt gebruikt en kies welke status u wilt weergeven.

    >[!div class="mx-imgBorder"] 
    >![Besturingselement Splitsen](media/split-by-interface.png)

8. Voeg voor de case **Smart Vitals Patch** de actie **JSON parseren** toe. Kies voor de inhoud **Eigenschappen**, afkomstig van de Event Hub. Kopieer en plak de voorbeelden van de nettoladingen voor de bovenstaande Smart Vitals Patch om het schema te genereren.

9. Voeg de actie **Variabele instellen** toe en werk de variabele **Hoofdtekst** bij met de **hoofdtekst** van de geparseerde JSON in stap 7.

10. Voeg het besturingselement **Voorwaarde** toe als de volgende actie en stel de voorwaarde in op **Hoofdtekst**, **bevat**, **HeartRate**. Dit zorgt ervoor dat u over de juiste set gegevens beschikt, afkomstig van de Smart Vitals Patch, voordat u de Power BI-gegevensset invult. Stappen 7 - 9 zien er als volgt uit:

    >[!div class="mx-imgBorder"] 
    >![Toevoegvoorwaarde in Smart Vitals](media/smart-vitals-pbi.png)

11. Voor de case **Waar** van de voorwaarde, voegt u een actie toe waarmee de Power BI-functionaliteit **Rijen toevoegen aan een gegevensset** wordt aangeroepen. U moet zich hiervoor aanmelden bij Power BI. De case **Onwaar** kan opnieuw gebruikmaken van het besturingselement **Beëindigen**.

12. Kies de juiste **werkruimte**, **gegevensset**en **tabel**. Wijs de parameters die u hebt opgegeven bij het maken van uw streaminggegevensset in Power BI, toe aan de geparseerde JSON-waarden die van de Event Hub afkomstig zijn. De ingevulde acties moeten er als volgt uitzien:

    >[!div class="mx-imgBorder"] 
    >![Rijen toevoegen aan Power BI](media/add-rows-yesenia.png)

13. Voor de case Schakelen van de **Smart Knee Brace** voegt u de actie **JSON parseren** toe om de inhoud te parseren, soortgelijk als in stap 7. Voeg vervolgens **Rijen toevoegen aan een gegevensset** toe om de gegevensset van Enver Jobse bij te werken in Power BI.

    >[!div class="mx-imgBorder"] 
    >![Toevoegvoorwaarde in Smart Vitals](media/knee-brace-pbi.png)

14. Druk op **Opslaan** en voer vervolgens uw logische app uit.

## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Een realtime dashboard bouwen voor vitale gegevens van patiënten
Ga terug naar Power BI en selecteer **+ Maken** om een nieuw **Dashboard** te maken. Geef het dashboard een naam en druk op **Maken**.

Selecteer de drie puntjes in de bovenste navigatiebalk en selecteer vervolgens **+ Tegel toevoegen**.

>[!div class="mx-imgBorder"] 
>![Tegel toevoegen aan dashboard](media/add-tile.png)

Kies het type tegel dat u wilt toevoegen en pas uw app naar wens aan.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijdert u de resources in de volgende stappen:

1. Vanuit Azure Portal kunt u de Event Hub- en Logic Apps-resources verwijderen die u hebt gemaakt.

2. Ga voor uw IoT Central-toepassing naar het tabblad Beheer en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

* Lees [Hulp bij architectuur voor continue patiëntbewaking](concept-continuous-patient-monitoring-architecture.md).

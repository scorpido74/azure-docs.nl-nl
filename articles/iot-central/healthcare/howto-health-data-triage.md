---
title: Een triage-dashboard voor statusgegevens maken met Azure IoT Central | Microsoft Documenten
description: Meer informatie over het bouwen van een dashboard voor het triagevan gegevens van statusgegevens met Azure IoT Central-toepassingssjablonen.
author: philmea
ms.author: philmea
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 99b27ec53d955079b5f73986408e698955c0969b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77021641"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Zelfstudie: Een dashboard van een Power BI-provider maken



Bij het bouwen van uw continue patiëntbewakingsoplossing u ook een dashboard maken voor een ziekenhuiszorgteam om patiëntgegevens te visualiseren. In deze zelfstudie leert u hoe u een Power BI-realtime streamingdashboard maakt op basis van uw iot central-sjabloon voor continue patiëntbewaking.

>[!div class="mx-imgBorder"]
>![Dashboard GIF](media/dashboard-gif-3.gif)

De basisarchitectuur volgt deze structuur:

>[!div class="mx-imgBorder"] 
>![Provider Triage-dashboard](media/dashboard-architecture.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gegevens exporteren vanuit Azure IoT Central naar Azure Event Hubs
> * Een Power BI-streaminggegevensset instellen
> * Uw Logic-app verbinden met Azure-gebeurtenishubs
> * Gegevens streamen naar Power BI vanuit uw Logic-app
> * Bouw een real-time dashboard voor de vitale functies van de patiënt

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een azure IoT Central-sjabloon voor continue patiëntbewaking. Als u er nog geen hebt, u stappen uitvoeren om [een toepassingssjabloon te implementeren.](overview-iot-central-healthcare.md)

* Een [naamruimte voor](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)Azure Event Hubs en gebeurtenishub .

* De Logische App die u wilt openen bij uw gebeurtenishub. Als u uw Logic App wilt starten met een Trigger voor Azure Event Hubs, hebt u een [lege Logische App](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow)nodig.

* Een Power BI-serviceaccount. Als u er nog geen hebt, u [een gratis proefaccount maken voor de Power BI-service.](https://app.powerbi.com/) Als u Power BI nog niet eerder hebt gebruikt, kan het handig zijn om aan de slag te gaan [met Power BI.](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Een continue gegevensexport naar Azure Event Hubs instellen
U moet eerst een continue gegevensexport instellen vanuit uw Azure IoT Central-appsjabloon naar de Azure Event Hub in uw abonnement. U dit doen door de stappen te volgen in deze azure IoT Central-zelfstudie voor [exporteren naar gebeurtenishubs.](https://docs.microsoft.com/azure/iot-central/core/howto-export-data) U hoeft alleen te exporteren voor de telemetrie voor de doeleinden van deze tutorial.

## <a name="create-a-power-bi-streaming-dataset"></a>Een Power BI-streaminggegevensset maken

1. Meld u aan bij uw Power BI-account.

2. Maak in de gewenste werkruimte een nieuwe streaminggegevensset door de knop **+ Maken** in de rechterbovenhoek van de werkbalk te selecteren. U moet een aparte gegevensset maken voor elke patiënt die u op uw dashboard wilt hebben.

    >[!div class="mx-imgBorder"] 
    >![Streaminggegevensset maken](media/create-streaming-dataset.png)

3. Kies **API** voor de bron van uw gegevensset.

4. Voer een **naam** in (bijvoorbeeld de naam van een patiënt) voor uw gegevensset en vul vervolgens de waarden van uw stream in. U hieronder een voorbeeld zien op basis van waarden die afkomstig zijn van de gesimuleerde apparaten in de sjabloon voor continue patiëntbewaking. Het voorbeeld heeft twee patiënten:

    * Teddy Silvers, die gegevens heeft van de Smart Knee Brace
    * Yesenia Sanford, die gegevens heeft van de Smart Vitals Patch

    >[!div class="mx-imgBorder"] 
    >![Gegevenssetwaarden invoeren](media/enter-dataset-values.png)

Voor meer informatie over streaming datasets in Power BI, u dit document lezen over [real-time streaming in Power BI](https://docs.microsoft.com/power-bi/service-real-time-streaming).

## <a name="connect-your-logic-app-to-azure-event-hubs"></a>Uw Logic-app verbinden met Azure-gebeurtenishubs
Als u uw Logic App wilt koppelen aan Azure Event Hubs, u de instructies volgen die in dit document worden beschreven voor [het verzenden van gebeurtenissen met Azure Event Hubs en Azure Logic Apps.](https://docs.microsoft.com/azure/connectors/connectors-create-api-azure-event-hubs#add-event-hubs-action) Hier zijn enkele voorgestelde parameters:

|Parameter|Waarde|
|---|---|
|Inhoudstype|application/json|
|Interval|3|
|Frequency|Seconde|

Aan het einde van deze stap moet uw Logic App Designer er als volgt uitzien:

>[!div class="mx-imgBorder"] 
>![Logic Apps maakt verbinding met gebeurtenishubs](media/eh-logic-app.png)

## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Gegevens streamen naar Power BI vanuit uw Logic-app
De volgende stap is om de gegevens van uw gebeurtenishub te ontleden om deze te streamen naar de Power BI-gegevenssets die u eerder hebt gemaakt.

1. Voordat u dit doen, moet u de JSON-payload begrijpen die van uw apparaat naar uw Event Hub wordt verzonden. U dit doen door naar dit [voorbeeldschema](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#telemetry) te kijken en het aan te passen aan uw schema of [door de verkenner servicebus](https://github.com/paolosalvatori/ServiceBusExplorer) te gebruiken om de berichten te inspecteren. Als u de continue toepassingen voor patiëntbewaking gebruikt, zien uw berichten er als volgt uit:

**Smart Vitals Patch telemetrie**

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

**Smart Knee Brace telemetrie**

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

2. Nu u uw JSON-payloads hebt geïnspecteerd, gaat u terug naar uw Logic App Designer en selecteert **u + Nieuwe stap.** Zoek en voeg **initialize variabele** toe als de volgende stap en voer de volgende parameters in:

    |Parameter|Waarde|
    |---|---|
    |Name|Interfacenaam|
    |Type|Tekenreeks|

    Druk op **Opslaan**. 

3. Voeg een andere variabele met de naam **Tekst** als **tekenreeks toe.** In uw Logic App worden deze acties toegevoegd:

    >[!div class="mx-imgBorder"]
    >![Variabelen initialiseren](media/initialize-string-variables.png)
    
4. Selecteer **+ Nieuwe stap** en voeg een **Parse JSON-actie** toe. Wijzig de naam van **deze naam in Parse Properties**. Kies **eigenschappen** die afkomstig zijn van de gebeurtenishub voor de inhoud. Selecteer **Voorbeeldpayload gebruiken om schema** onderaan te genereren en plak het monsterpayload in de sectie Eigenschappen hierboven.

5. Kies vervolgens de variabele actie **Instellen** en werk de variabele **Interfacenaam** bij met de **iothub-interfacenaam** van de geparseerde JSON-eigenschappen.

6. Voeg een **gesplitst** besturingselement toe als uw volgende actie en kies de variabele **Interfacenaam** als parameter Aan. U gebruikt dit om de gegevens naar de juiste gegevensset te trechteren.

7. Zoek in uw Azure IoT Central-toepassing de interfacenaam voor de statusgegevens smart vitals patch en de statusgegevens van Smart Knee Brace in de weergave **Apparaatsjablonen.** Maak twee verschillende hoesjes voor het **switchbesturingselement** voor elke interfacenaam en wijzig de naam van het besturingselement op de juiste manier. U de standaardaanvraag zo instellen dat u het **besturingselement beëindigen** gebruikt en kiezen welke status u wilt weergeven.

    >[!div class="mx-imgBorder"] 
    >![Besturingselement splitsen](media/split-by-interface.png)

8. Voor de **Smart Vitals** Patch-hoes voegt u een **Parse JSON-actie** toe. Kies voor de inhoud **Inhoud** die afkomstig is van de gebeurtenishub. Kopieer en plak de sample payloads voor de Smart Vitals Patch hierboven om het schema te genereren.

9. Voeg een variabele actie **Instellen** toe en werk de **lichaamsvariabele** bij met de **body** van de geparseerde JSON in stap 7.

10. Voeg een **condition** Control toe als uw volgende actie en stel de aandoening in **op Body**, **bevat** **, HeartRate**. Dit zorgt ervoor dat u de juiste set gegevens uit de Smart Vitals Patch hebt voordat u de Power BI-gegevensset bevolkt. Stap 7-9 ziet er als volgt uit:

    >[!div class="mx-imgBorder"] 
    >![Smart Vitals toevoegen conditie](media/smart-vitals-pbi.png)

11. Voeg voor het geval **Waar** van de voorwaarde een actie toe die de **power bi-functionaliteit toevoegen aan een gegevensset** aanroept. Hiervoor moet u zich aanmelden bij Power BI. Uw **foutieve** aanvraag kan opnieuw het **besturingselement Beëindigen** gebruiken.

12. Kies de juiste **werkruimte**, **gegevensset**en **tabel**. Wijs de parameters die u hebt opgegeven bij het maken van uw streaminggegevensset in Power BI toe aan de geparseerde JSON-waarden die afkomstig zijn van uw gebeurtenishub. Uw ingevulde acties moeten er als volgt uitzien:

    >[!div class="mx-imgBorder"] 
    >![Rijen toevoegen aan Power BI](media/add-rows-yesenia.png)

13. Voeg voor de **Smart Knee Brace-switchcase** een **Parse JSON-actie** toe om de inhoud te ontleden, vergelijkbaar met stap 7. Voeg **vervolgens rijen toe aan een gegevensset** om de gegevensset Teddy Silvers in Power BI bij te werken.

    >[!div class="mx-imgBorder"] 
    >![Smart Vitals toevoegen conditie](media/knee-brace-pbi.png)

14. Druk op **Opslaan** en voer vervolgens uw Logic-app uit.

## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Bouw een real-time dashboard voor de vitale functies van de patiënt
Ga nu terug naar Power BI en selecteer **+ Maak om** een nieuw **dashboard**te maken. Geef uw dashboard een naam en druk op **Maken**.

Selecteer de drie puntjes in de bovenste navigatiebalk en selecteer **+ Tegel toevoegen**.

>[!div class="mx-imgBorder"] 
>![Tegel toevoegen aan dashboard](media/add-tile.png)

Kies het type tegel dat u wilt toevoegen en pas uw app aan zoals u dat wilt.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet blijft gebruiken, verwijdert u uw bronnen met de volgende stappen:

1. Vanuit de Azure-portal u de resources Event Hub en Logic Apps die u hebt gemaakt, verwijderen.

2. Ga voor uw IoT Central-toepassing naar het tabblad Beheer en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

* Bekijk de [continue richtlijnen voor de architectuur van de patiëntmonitoring.](concept-continuous-patient-monitoring-architecture.md)

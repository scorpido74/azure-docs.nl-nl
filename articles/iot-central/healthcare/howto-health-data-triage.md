---
title: Een sorteren-dash board met status gegevens maken met Azure IoT Central | Microsoft Docs
description: Meer informatie over het bouwen van een sorteren-dash board met status gegevens met behulp van Azure IoT Central-toepassings sjablonen.
author: philmea
ms.author: philmea
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: c30f939aa353ae5809a8ba6e9c2b2d7f376ddc0d
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72956661"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Zelf studie: een Power BI provider-dash board maken

Wanneer u uw oplossing voor continue patiënten-bewaking bouwt, kunt u ook een dash board maken voor een ziekenhuis Care team om patiënten-gegevens te visualiseren. Deze zelf studie begeleidt u stapsgewijs door de stappen voor het maken van een Power BI realtime streaming-dash board vanuit uw IoT Central toepassing voor continue patiënten-bewaking.

>[!div class="mx-imgBorder"]
>GIF-](media/dashboard-gif-3.gif) ![dash board

De basis architectuur volgt deze structuur:

>[!div class="mx-imgBorder"] 
>Sorteren-dash board van ![provider](media/dashboard-architecture.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gegevens exporteren van Azure IoT Central naar Azure Event Hubs
> * Een gegevensset voor Power BI stream instellen
> * Uw logische app verbinden met Azure Event Hubs
> * Gegevens streamen naar Power BI vanuit uw logische app
> * Een real-time dash board bouwen voor vitale patiënten

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een Azure IoT Central-sjabloon voor continue patiënten-bewakings toepassingen. Als u er nog geen hebt, kunt u de stappen voor [het implementeren van een toepassings sjabloon](overview-iot-central-healthcare.md)volgen.

* Een Azure [Event hubs-naam ruimte en Event hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

* De logische app die u wilt gebruiken voor toegang tot uw event hub. Als u uw logische app wilt starten met een Azure Event Hubs-trigger, hebt u een [lege logische app](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow)nodig.

* Een Power BI-service-account. Als u er nog geen hebt, kunt u [een gratis proef account maken voor Power bi-service](https://app.powerbi.com/). Als u Power BI nog niet eerder hebt gebruikt, kan het nuttig zijn om aan de slag te gaan [met Power bi](https://docs.microsoft.com/power-bi/service-get-started).

## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Een continue gegevens export naar Azure Event Hubs instellen
U moet eerst een continue gegevens export instellen vanuit uw Azure IoT Central app-sjabloon naar de Azure Event hub in uw abonnement. U kunt dit doen door de stappen in deze Azure IoT Central-zelf studie [te volgen om naar Event hubs te exporteren](https://docs.microsoft.com/azure/iot-central/howto-export-data-event-hubs-service-bus-pnp). Voor de doel einden van deze zelf studie hoeft u alleen voor de telemetrie te exporteren.

## <a name="create-a-power-bi-streaming-dataset"></a>Een gegevensset voor Power BI stream maken

1. Meld u aan bij uw Power BI-account.

2. Maak in de gewenste werk ruimte een nieuwe streaming-gegevensset door de knop **+ maken** te selecteren in de rechter bovenhoek van de werk balk. U moet een afzonderlijke gegevensset maken voor elke patiënt die u op uw dash board wilt hebben.

    >[!div class="mx-imgBorder"] 
    >streaming-gegevensset ![maken](media/create-streaming-dataset.png)

3. Kies een **API** voor de bron van de gegevensset.

4. Voer een **naam** (bijvoorbeeld de naam van een patiënt) in voor uw gegevensset en vul vervolgens de waarden in vanuit uw stream. U kunt hieronder een voor beeld zien op basis van waarden die afkomstig zijn van de gesimuleerde apparaten in de sjabloon continue patiënten-bewakings toepassing. Het voor beeld heeft twee patiënten:

    * Teddy Silvers, die gegevens uit de slimme knie accolade bevat
    * Yesenia Sanford, die gegevens bevat uit de Smart vitale patch

    >[!div class="mx-imgBorder"] 
    >de waarden van de gegevensset ![invoeren](media/enter-dataset-values.png)

Als u meer wilt weten over het streamen van gegevens sets in Power BI, kunt u dit document lezen in [realtime streaming in Power bi](https://docs.microsoft.com/power-bi/service-real-time-streaming).

## <a name="connect-your-logic-app-to-azure-event-hubs"></a>Uw logische app verbinden met Azure Event Hubs
Als u uw logische app wilt verbinden met Azure Event Hubs, kunt u de instructies in dit document volgen op het [verzenden van gebeurtenissen met Azure Event hubs en Azure Logic apps](https://docs.microsoft.com/azure/connectors/connectors-create-api-azure-event-hubs#add-event-hubs-action). Hier volgen enkele aanbevolen para meters:

|Parameter|Waarde|
|---|---|
|Inhouds type|application/json|
|Interval|3|
|Frequency|Seconde|

Aan het einde van deze stap moet de ontwerp functie voor logische apps er als volgt uitzien:

>[!div class="mx-imgBorder"] 
>![Logic Apps verbinding maken met Event Hubs](media/eh-logic-app.png)

## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Gegevens streamen naar Power BI vanuit uw logische app
De volgende stap is het parseren van de gegevens die afkomstig zijn van uw event hub om deze te streamen naar de Power BI gegevens sets die u eerder hebt gemaakt.

1. Voordat u dit kunt doen, moet u weten wat de JSON-nettolading is die vanaf uw apparaat wordt verzonden naar uw event hub. U kunt dit doen door te kijken naar dit [voorbeeld schema](https://docs.microsoft.com/azure/iot-central/howto-export-data-event-hubs-service-bus-pnp#telemetry) en dit te wijzigen zodat dit overeenkomt met uw schema of [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) gebruiken om de berichten te controleren. Als u de toepassingen doorlopende patiënten-bewaking gebruikt, ziet uw berichten er als volgt uit:

**Intelligente essentiële patch-telemetrie**

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

**Slimme knie accolade-telemetrie**

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

2. Nu u uw JSON-nettoladingen hebt geïnspecteerd, gaat u terug naar de ontwerp functie voor logische apps en selecteert u **+ nieuwe stap**. Zoek de **variabele Initialize** en voeg deze toe als uw volgende stap en voer de volgende para meters in:

    |Parameter|Waarde|
    |---|---|
    |Naam|Interface naam|
    |Type|Tekenreeks|

    Druk op **Opslaan**. 

3. Voeg een andere variabele met de naam **Body** toe met het type **teken reeks**. Deze acties worden toegevoegd aan de logische app:

    >[!div class="mx-imgBorder"]
    >![variabelen initialiseren](media/initialize-string-variables.png)
    
4. Selecteer **+ nieuwe stap** en voeg een **JSON-actie parseren** toe. Wijzig de naam van deze om **Eigenschappen te parseren**. Kies voor de inhoud **Eigenschappen** die afkomstig zijn van de Event hub. Selecteer **voor beeld-Payload gebruiken om een schema onderaan te genereren** en plak de voor beeld-nettolading uit de sectie eigenschappen hierboven.

5. Kies vervolgens de actie **variabele instellen** en werk de variabele **interface naam** bij met de **iothub-interface-naam** van de geparseerde JSON-eigenschappen.

6. Voeg een **Splits** Control toe als de volgende actie en kies de variabele **interface naam** als de para meter bij. U gebruikt deze om de gegevens naar de juiste gegevensset te trechteren.

7. Zoek in uw Azure IoT Central-toepassing de naam van de interface voor de Smart vitale patch Health-gegevens en de info over de status van de Smart-beugels in de weer gave **apparaat-sjablonen** . Maak twee verschillende cases voor het besturings element **Switch** voor elke interface naam en wijzig de naam van het besturings element. U kunt de standaard Case instellen op het gebruik van de controle **beëindigen** en bepalen welke status u wilt weer geven.

    >[!div class="mx-imgBorder"] 
    >gesplitste besturings elementen ![](media/split-by-interface.png)

8. Voeg een JSON-actie voor **parsering** toe aan de **Smart vitale patch** case. Kies voor de inhoud de **inhoud** die afkomstig is van de Event hub. Kopieer de voor beelden van nettoladingen en plak deze voor de bovenstaande patch voor het maken van een essentiële update om het schema te genereren.

9. Voeg een actie **variabele instellen** toe en werk de **hoofdtekst** variabele bij met de **hoofd tekst** van de geparseerde json in stap 7.

10. Voeg een besturings element **voor waarde** toe als uw volgende actie en stel de voor waarde in op **Body**, **contains**, **HeartRate**. Dit zorgt ervoor dat u beschikt over de juiste set gegevens die afkomstig zijn uit de Smart vitale patch voordat u de Power BI gegevensset invult. De stappen 7-9 ziet er als volgt uit:

    >[!div class="mx-imgBorder"] 
    >voor waarde ![slimme vitale waarden toevoegen](media/smart-vitals-pbi.png)

11. Voor het **geval van de voor** waarde voegt u een actie toe die de functionaliteit **rijen toevoegen aan een gegevensset** Power bi aanroept. U moet zich hiervoor aanmelden bij Power BI. Met de **fout melding kunt u het** besturings element **beëindigen** opnieuw gebruiken.

12. Kies de juiste **werk ruimte**, **gegevensset**en **tabel**. U kunt de para meters die u hebt opgegeven tijdens het maken van uw streaming-gegevensset in Power BI toewijzen aan de geparseerde JSON-waarden die afkomstig zijn van uw event hub. De ingevulde acties moeten er als volgt uitzien:

    >[!div class="mx-imgBorder"] 
    >![rijen toevoegen aan Power BI](media/add-rows-yesenia.png)

13. Voor de Smart-Schakel optie voor de **knie-accolades** voegt u een JSON-actie **parser** toe om de inhoud te parseren, vergelijkbaar met stap 7. **Voeg vervolgens rijen toe aan een gegevensset** om uw Teddy Silvers-gegevensset bij te werken in Power bi.

    >[!div class="mx-imgBorder"] 
    >voor waarde ![slimme vitale waarden toevoegen](media/knee-brace-pbi.png)

14. Druk op **Opslaan** en voer vervolgens uw logische app uit.

## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Een real-time dash board bouwen voor vitale patiënten
Ga nu terug naar Power BI en selecteer **+ maken** om een nieuw **dash board**te maken. Geef uw dash board een naam en druk op **maken**.

Selecteer de drie puntjes in de bovenste navigatie balk en selecteer vervolgens **+ tegel toevoegen**.

>[!div class="mx-imgBorder"] 
>![tegel toevoegen aan dash board](media/add-tile.png)

Kies het type tegel dat u wilt toevoegen en uw app aan te passen.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet wilt blijven gebruiken, verwijdert u uw resources met de volgende stappen:

1. Vanuit het Azure Portal kunt u de Event hub verwijderen en Logic Apps resources die u hebt gemaakt.

2. Ga voor uw IoT Central-toepassing naar het tabblad beheer en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

* Bekijk de [richt lijnen voor de architectuur van voortdurende patiënten-bewaking](concept-continuous-patient-monitoring-architecture.md).

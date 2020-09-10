---
title: 'Zelfstudie: Ruimtelijke IoT-analyse implementeren met Microsoft Azure Maps'
description: Integreer IoT Hub met API’s van de Microsoft Azure Maps-service.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4150464b5c59b631afea0c788b1e351dee5185f9
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299371"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Zelfstudie: Ruimtelijke IoT-analyse implementeren met behulp van Azure Maps

In een IoT-scenario is het gebruikelijk om relevante gebeurtenissen die zich in ruimte en tijd voordoen, vast te leggen en bij te houden. Voorbeeldscenario's omvatten wagenparkbeheer, assets bijhouden, mobiliteit, en Smart City-toepassingen. Deze zelfstudie begeleidt u door een oplossing waarmee het verplaatsen van tweedehandshuurauto’s wordt getraceerd, met behulp van de Azure Maps-API’s.

In deze zelfstudie gaat u:

> [!div class="checklist"]
> * Een Azure Storage-account maken om traceringsgegevens voor auto’s te registreren.
> * Een geofence uploaden in de gegevensservices van Azure Maps met behulp van de Data Upload-API.
> * Een IoT-hub maken en uw apparaat registreren.
> * Een functie maken in Azure Functions, waarbij u bedrijfslogica implementeert op basis van de ruimtelijke analyse van Azure Maps.
> * U abonneren op telemetriegebeurtenissen voor IoT-apparaten vanuit de Azure-functie via Event Grid.
> * De telemetriegebeurtenissen filteren met behulp van berichtroutering van IoT Hub.

## <a name="prerequisites"></a>Vereisten

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. [Maak een Azure Maps-account](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel. Zie [Verificatie beheren in Azure Maps](how-to-manage-authentication.md) voor meer informatie over verificatie in Azure Maps.

4. [Maak een resourcegroep.](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups) In deze zelfstudie noemen we de resourcegroep *ContosoRental*, maar u kunt elke gewenste naam kiezen.

5. Download het [C#-project rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

In deze zelfstudie wordt gebruikgemaakt van de [Postman](https://www.postman.com/)-toepassing, maar u kunt ook een andere API-ontwikkelomgeving kiezen.

## <a name="use-case-rental-car-tracking"></a>Use-case: huurauto traceren

In deze zelfstudie wordt het volgende scenario gedemonstreerd: Een autoverhuurbedrijf wil locatiegegevens, reisafstand en actieve status voor de huurauto's van het bedrijf registreren. Het bedrijf wil deze informatie opslaan telkens wanneer een auto de correcte geautoriseerde geografische regio verlaat.

In onze use-case zijn de huurauto's uitgerust met IoT-apparaten die regelmatig telemetriegegevens naar Azure IoT Hub verzenden. De telemetrie bevat de huidige locatie en geeft aan of de motor van de auto loopt. Het apparaatlocatieschema houdt zich aan het IoT [Plug and Play-schema voor georuimtelijke gegeven](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). Het schema voor de apparaattelemetrie van de huurauto ziet eruit als de volgende JSON-code:

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": {
            "location": {
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            }
        }
    }
}
```

In deze zelfstudie wordt één voertuig gevolgd. Nadat de Azure-services zijn ingesteld, moet u het [C#-project rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) downloaden om de voertuigsimulator uit te voeren. Het hele proces, van gebeurtenis tot uitvoering van de functie, wordt samengevat in de volgende stappen:

1. Het apparaat in het voertuig verzendt telemetriegegevens naar een IoT-hub.

2. Als de motor van de auto loopt, publiceert de IoT-hub de telemetriegegevens in de Event Grid.

3. Er wordt een Azure-functie geactiveerd vanwege het bijbehorende gebeurtenisabonnement op telemetriegebeurtenissen van het apparaat.

4. Met de functie worden de locatiecoördinaten van het voertuigapparaat, de gebeurtenistijd, en de apparaat-id geregistreerd. Vervolgens wordt de [Ruimtelijke Geofence Get API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) gebruikt om te bepalen of de auto zich buiten de geofence heeft begeven. Als de auto buiten de grenzen van de geofence is geweest, worden de locatiegegevens die zijn ontvangen van de gebeurtenis, met de functie opgeslagen in onze blobcontainer. Via de functie wordt ook de [omgekeerde zoekopdracht voor adressen](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) uitgevoerd om de locatiecoördinaten om te zetten in een straatnaam, en deze op te slaan bij de andere locatiegegevens voor het apparaat.

Het volgende diagram geeft je een overzicht op hoog niveau van het systeem.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="Systeemoverzicht":::

In de volgende afbeelding is het geofence-gebied blauw gemarkeerd. De route van de huurauto wordt aangegeven met een groene lijn.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Geofence-route":::

## <a name="create-an-azure-storage-account"></a>Een Azure-opslagaccount maken

Om de traceringsgegevens over schendingen van de auto op te slaan maken we een [algemeen v2-opslagaccount](https://docs.microsoft.com/azure/storage/common/storage-account-overview#general-purpose-v2-accounts) in de resourcegroep. Als u nog geen resourcegroep hebt gemaakt, volgt u de instructies in [Een resourcegroep maken](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups). In deze zelfstudie noemen we de resourcegroep *ContosoRental*.

Volg de instructies in [Een opslagaccount maken](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal) om een opslagaccount te maken. In deze zelfstudie noemen we het opslagaccount *contosorentalstorage*, maar u kunt elke gewenste naam kiezen.

Zodra het opslagaccount is gemaakt, moet u een container maken om logboekgegevens in op te slaan.

1. Ga naar het zojuist gemaakte opslagaccount. Klik op de koppeling **Containers** in de sectie Essentials.

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Containers voor blobopslag":::

2. Klik op de knop **+ Container** in de linkerbovenhoek. Aan de rechterkant van de browser wordt een paneel weergegeven. Geef de container de naam *contoso-rental-logs* en klik op **Maken**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Een blobcontainer maken":::

3. Ga naar de blade **Toegangssleutels** in uw opslagaccount, en kopieer de waarden bij **Opslagaccountnaam** en **Sleutel** in de sectie **key1**. Beide waarden hebben we nodig in de sectie [Een Azure-functie maken en een Event Grid-abonnement toevoegen](#create-an-azure-function-and-add-an-event-grid-subscription).

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="De naam en sleutel van het opslagaccount kopiëren":::

## <a name="upload-a-geofence"></a>Een geofence uploaden

Nu gebruiken we de [Postman-app](https://www.getpostman.com) om [de geofence te uploaden](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) naar de Azure Maps-service. De geofence definieert het toegestane geografische gebied voor het huurvoertuig.  We gebruiken de geofence in de Azure-functie om te bepalen of een auto is verplaatst buiten het geofence-gebied.

Open de Postman-app en volg de onderstaande stappen om de geofence te uploaden met behulp van de Azure Maps Data Upload-API.  

1. Open de Postman-app. Selecteer **New** (Nieuw) bovenaan de Postman-app. Selecteer **Collection** (Verzameling) in het venster **Create New** (Nieuwe maken).  Geef de verzameling een naam en selecteer de knop **Create** (Maken).

2. Selecteer nogmaals **New** (Nieuw) om de aanvraag te maken. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **Request name** (Aanvraagnaam) in voor de aanvraag. Selecteer de verzameling die u in de vorige stap hebt gemaakt en selecteer **Save** (Opslaan).

3. Selecteer de HTTP-methode **POST** op het tabblad Builder en voer de volgende URL in om de geofence te uploaden naar de Data Upload-API. Vergeet niet om `{subscription-key}` te vervangen door de primaire abonnementssleutel.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    De waarde "geojson" naast de parameter `dataFormat` in het URL-pad vertegenwoordigt de indeling van de gegevens die worden geüpload.

4. Klik op **Hoofdtekst** en selecteer vervolgens de **onbewerkte** invoerindeling. Kies de invoerindeling **JSON** in de vervolgkeuzelijst. Open [hier](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4) het JSON-gegevensbestand, en kopieer het bestand in de sectie met de hoofdtekst. Klik op **Verzenden**.

5. Klik op de blauwe **Send**-knop (Verzenden) en wacht tot de aanvraag is verwerkt. Zodra de aanvraag is voltooid, gaat u naar het tabblad **Headers** (Kopteksten) van het antwoord. Kopieer de waarde van de **Location**-sleutel (Locatie), de `status URL`.

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Maak een **GET** HTTP-aanvraag op de `status URL` om de status van de API-aanroep te controleren. U moet uw primaire abonnementssleutel toevoegen aan de URL voor authenticatie. De **GET**-aanvraag moet lijken op de volgende URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}

7. When the **GET** HTTP request completes successfully, it will return a `resourceLocation`. The `resourceLocation` contains the unique `udid` for the uploaded content. You'll need to copy this `udid` for later use in this tutorial.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-azure-iot-hub"></a>Een Azure IoT-hub maken

Azure IoT Hub maakt veilige en betrouwbare communicatie in twee richtingen mogelijk tussen een IoT-toepassing en de beheerde apparaten.  In ons scenario willen we informatie ontvangen van het apparaat in het voertuig om zo de locatie van de huurauto te bepalen. In deze sectie maken we een IoT-hub in de resourcegroep *ContosoRental*. De IoT-hub is verantwoordelijk voor het publiceren van de telemetriegebeurtenissen van het apparaat.

> [!NOTE]
> De functionaliteit van IoT Hub om telemetriegebeurtenissen van apparaten op Event Grid te publiceren, is in openbare preview. Openbare preview-functies zijn beschikbaar in alle regio's behalve **Oost-VS, West-VS, West-Europa, Azure Government, Azure China 21Vianet** en **Azure Germany**.

Als u een Iot-hub wilt maken in de resourcegroep *ContosoRental*, volgt u de stappen in [Een IoT-hub maken](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).

## <a name="register-a-device-in-iot-hub"></a>Een apparaat registreren in IoT Hub

Apparaten kunnen geen verbinding maken met de IoT-hub, tenzij ze zijn geregistreerd in het IoT Hub-identiteitsregister. In ons scenario maakt u één apparaat met de naam *InVehicleDevice*. Als u het apparaat wilt maken en registreren in IoT Hub, volgt u de stappen in [Een nieuw apparaat registreren in de IoT-hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#register-a-new-device-in-the-iot-hub). Zorg ervoor dat u de **Primaire verbindingsreeks** van het apparaat kopieert, omdat deze in een latere stap wordt gebruikt.

## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Maak een Azure-functie en voeg een Event Grid-abonnement toe

Azure Functions is een serverloze rekenservice waarmee u kleine codefragmenten (functies) kunt uitvoeren, zonder dat u de rekeninfrastructuur expliciet hoeft in te richten of te beheren. Raadpleeg de documentatie voor [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) voor meer informatie over Azure Functions.

Een functie wordt geactiveerd via een bepaalde gebeurtenis. In ons scenario maakt u een functie die wordt geactiveerd met een Event Grid-trigger. U maakt de relatie tussen de trigger en de functie door een gebeurtenisabonnement te maken voor de telemetriegebeurtenissen van het IoT Hub-apparaat. Wanneer een telemetriegebeurtenis van het apparaat optreedt, wordt de functie aangeroepen als eindpunt, en ontvangt deze de relevante gegevens voor het [apparaat dat we eerder hebben geregistreerd in IoT Hub](#register-a-device-in-iot-hub).

[Hier](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) ziet u de C#-scriptcode die onze functie bevat.

Nu gaan we de Azure-functie instellen.

1. Klik op het dashboard in de Azure-portal op **Een resource maken**. Typ in het tekstvak: **Functie-app**. Klik op **Functie-app**. Klik op **Create**.

2. Geef de functie-app een naam op de aanmaakpagina van **Functie-app**. Selecteer onder **Resourcegroep** de optie *ContosoRental* in de vervolgkeuzelijst.  Selecteer *.NET Core* als **Runtimestack**. Klik op **Next: Hosten >** onderaan de pagina.

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Een functie-app maken":::

3. Selecteer voor **Opslagaccount** het opslagaccount dat u hebt gemaakt in [Een Azure-opslagaccount maken](#create-an-azure-storage-account). Klik op **Controleren + maken**.

4. Bekijk de details van de functie-app en klik op **Maken**.

5. Zodra de app is gemaakt, voegen we een functie toe. Ga naar functie app. Klik op de blade **Functies**. Klik bovenaan de pagina op **+ Toevoegen**. Het paneel met functiesjablonen wordt weergegeven. Schuif omlaag in het paneel. Klik op **Azure Event Grid-trigger**.

     >[!WARNING]
    > De namen van de sjablonen **Azure Event Hub-trigger** en **Azure Event Grid-trigger** lijken op elkaar. Zorg ervoor dat u op de sjabloon **Azure Event Grid-trigger** klikt.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Een functie maken":::

6. Geef een naam op voor de functie. In deze zelfstudie gebruiken we de naam *GetGeoFunction*, maar u kunt elke gewenste naam gebruiken. Klik op **Functie maken**.

7. Klik op de blade **Code + Test** in het menu aan de linkerkant. Kopieer en plak het [C#-script](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) in het codevenster.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Code kopiëren/plakken in het functievenster":::

8. Vervang de volgende parameters in de C#-code. Klik op **Opslaan**. Klik nog niet op **Testen/Uitvoeren**
    * Vervang **SUBSCRIPTION_KEY** door de primaire abonnementssleutel van uw Azure Maps-account.
    * Vervang **UDID** door de `udid` van de geofence die u hebt geüpload in [Een geofence uploaden](#upload-a-geofence).
    * De functie **CreateBlobAsync** in het script maakt een blob per gebeurtenis in het gegevensopslagaccount. Vervang de **ACCESS_KEY**, **ACCOUNT_NAME** en **STORAGE_CONTAINER_NAME** door de toegangssleutel, accountnaam en gegevensopslagcontainer van je opslagaccount. Deze waarden zijn gegenereerd tijdens het maken van het opslagaccount in [Een Azure-opslagaccount maken](#create-an-azure-storage-account).

9. Klik op de blade **Integratie** in het menu aan de linkerkant. Klik in het diagram op **Event Grid-trigger**. Typ een naam voor de trigger, bijvoorbeeld *eventCarTelemetry*, en klik op **Event Grid-abonnement maken**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Gebeurtenisabonnement toevoegen":::

10. Vul de abonnementsgegevens in. Geef het gebeurtenisabonnement een naam. Selecteer bij *Gebeurtenisschema* de optie *Gebeurtenisrasterschema*. Selecteer bij **Onderwerptypen** de optie *Azure IoT Hub-accounts*. Selecteer bij **Resourcegroep** de resourcegroep die u hebt gemaakt aan het begin van deze zelfstudie. Selecteer bij **Resource** de IoT-hub die u hebt gemaakt in [Een Azure IoT-hub maken](#create-an-azure-iot-hub). Selecteer bij **Filteren op gebeurtenistype** de optie *Apparaattelemetrie*. Nadat u deze opties hebt gekozen, ziet u dat **Onderwerptype** is gewijzigd in *IoT Hub*. Voor **Systeemonderwerpnaam** kunt u dezelfde naam gebruiken als voor uw resource.  Klik ten slotte in de sectie **Eindpuntgegevens** op **Een eindpunt selecteren**. Accepteer alle instellingen en klik op **Selectie bevestigen**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Gebeurtenisabonnement maken":::

11. Controleer de instellingen. Zorg ervoor dat het eindpunt de functie opgeeft die u in het begin van deze sectie hebt gemaakt. Klik op **Create**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Bevestiging voor Gebeurtenisabonnement maken":::

12. Nu bent u terug bij het paneel **Trigger bewerken**. Klik op **Opslaan**.

## <a name="filter-events-using-iot-hub-message-routing"></a>De gebeurtenissen filteren met behulp van berichtroutering van IoT Hub

Wanneer u een Event Grid-abonnement toevoegt aan de Azure-functie, wordt automatisch een berichtroute gemaakt in de opgegeven IoT-hub. Met berichtroutering kunt u verschillende gegevenstypen naar meerdere eindpunten routeren. Je kunt bijvoorbeeld telemetrieberichten van apparaten, levenscyclusgebeurtenissen van apparaten en dubbele wijzigingen van apparaten routeren. Voor meer informatie over IoT Hub-berichtenroutering ga je naar [IoT Hub-berichtroutering gebruiken](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Berichtroutering in IoT Hub":::

In het voorbeeldscenario willen we alleen berichten ontvangen wanneer de huurauto wordt verplaatst. We maken een routeringsquery om de gebeurtenissen te filteren waarbij de eigenschap `Engine` gelijk is aan **ON**. Als je een routeringsquery wilt maken, klik je op de **RouteToEventGrid**-route, vervang je de **Routing-query** door **"Engine = 'ON'"** en klik je op **Opslaan**. Nu zal de IoT-hub alleen apparaattelemetrie publiceren als de engine is ingeschakeld.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Routeringsberichten filteren":::

>[!TIP]
>Er zijn verschillende manieren om IoT-apparaat-naar-cloud-berichten te doorzoeken. Voor meer informatie over de syntaxis van berichtroutering ga je naar [IoT Hub-berichtroutering](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax).

## <a name="send-telemetry-data-to-iot-hub"></a>Stuur telemetriegegevens naar IoT Hub

Zodra de Azure-functie actief is, kunnen we telemetriegegevens verzenden naar de IoT-hub, vanwaaruit ze naar het Event Grid worden gerouteerd. Laten we een C#-applicatie gebruiken om locatiegegevens te simuleren voor een in-voertuig apparaat van een huurauto. Om de applicatie uit te voeren, heb je .NET Core SDK 2.1.0 of hoger nodig op je ontwikkelingsmachine. Volg de onderstaande stappen om gesimuleerde telemetriegegevens naar IoT Hub te verzenden.

1. Als u dit nog niet hebt gedaan, downloadt u het C#-project [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

2. Open het simulatedCar.cs-bestand in een teksteditor naar keuze en vervang de waarde van de `connectionString` door degene die je hebt opgeslagen toen je het apparaat registreerde en sla de wijzigingen in het bestand op.

3. Zorg ervoor dat .NET Core op je computer is geïnstalleerd. Navigeer in je lokale terminalvenster naar de hoofdmap van het C#-project en voer de volgende opdracht uit om de vereiste pakketten voor gesimuleerde apparaattoepassing te installeren:

    ```cmd/sh
    dotnet restore
    ```

4. Voer in dezelfde terminal de volgende opdracht uit om de simulatietoepassing voor huurauto's te bouwen en uit te voeren:

    ```cmd/sh
    dotnet run
    ```

  Je lokale terminal zou er als volgt uit moeten zien.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Terminal uitvoer":::

Als u nu de blobopslagcontainer opent, ziet u vier blobs voor locaties waar het voertuig zich buiten de geofence bevond.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Blobs in een container weergeven":::

Op de onderstaande kaart worden vier locatiepunten van het voertuig weergegeven die buiten de geofence vallen. Elke locatie is met regelmatige tijdsintervallen geregistreerd.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Kaart met schendingen":::

## <a name="explore-azure-maps-and-iot"></a>Azure Maps en IoT verkennen

Voor meer informatie over het verkennen van Azure Maps API's die in deze zelfstudie worden gebruikt, ga naar:

* [Zoekadres omkeren](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Geofence downloaden](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Voor een volledige lijst van Azure Maps REST API's, bekijk:

* [Azure Maps REST API's](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Voor meer informatie over IoT Plug and Play, bekijk:

* [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp)

Voor een lijst met apparaten die Azure-gecertificeerd zijn voor IoT ga je naar:

* [Azure-gecertificeerde apparaten](https://catalog.azureiotsolutions.com/)

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het verzenden van apparaat naar cloud-telemetrie en omgekeerd, bekijk:

> [!div class="nextstepaction"]
> [Telemetrie verzenden vanaf een apparaat](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

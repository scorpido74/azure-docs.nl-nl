---
title: 'Zelfstudie: Ruimtelijke IoT-analyse implementeren | Microsoft Azure Maps'
description: Integreer IoT Hub met API’s van de Microsoft Azure Maps-service.
author: philmea
ms.author: philmea
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: cf1d732391f86bec9c0ec2de1e6bace2e808bb19
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85318923"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Zelfstudie: Ruimtelijke IoT-analyse implementeren met behulp van Azure Maps

In een IoT-scenario is het gebruikelijk om relevante gebeurtenissen die zich in ruimte en tijd voordoen, vast te leggen en bij te houden. Voorbeeldscenario's omvatten wagenparkbeheer, assets bijhouden, mobiliteit, en Smart City-toepassingen. Deze zelfstudie begeleidt u door een oplossingspatroon met behulp van de Azure Maps-API’s. Relevante gebeurtenissen worden vastgelegd met IoT Hub, met behulp van het model voor gebeurtenisabonnementen dat wordt verschaft via Event Grid.

In deze zelfstudie gaat u:

> [!div class="checklist"]
> * Een IoT Hub maken.
> * Een geofence-gebied uploaden in de gegevensservices van Azure Maps met behulp van de Data Upload-API.
> * Een functie maken in Azure Functions, waarbij u bedrijfslogica implementeert op basis van de ruimtelijke analyse van Azure Maps.
> * U abonneren op telemetriegebeurtenissen voor IoT-apparaten vanuit de Azure-functie via Event Grid.
> * De telemetriegebeurtenissen filteren met behulp van berichtroutering van IoT Hub.
> * Een opslagaccount maken om relevante gebeurtenisgegevens op te slaan.
> * Een IoT-apparaat in een voertuig simuleren.
    

## <a name="use-case"></a>Toepassing

In deze oplossing wordt een scenario gedemonstreerd waarbij een autoverhuurbedrijf plant om gebeurtenissen te bewaken en registreren voor hun huurauto’s. Autoverhuurbedrijven verhuren auto's meestal in een specifieke geografische regio. Ze moeten de bijhouden waar de auto’s zich tijdens de verhuurperiode bevinden. Instanties van een auto die de gekozen geografische regio verlaat, moeten worden geregistreerd. Logboekgegevens zorgen ervoor dat het beleid, de vergoedingen en andere zakelijke aspecten correct worden afgehandeld.

In onze use-case zijn de huurauto's uitgerust met IoT-apparaten die regelmatig telemetriegegevens naar Azure IoT Hub verzenden. De telemetrie bevat de huidige locatie en geeft aan of de motor van de auto loopt. Het apparaatlocatieschema houdt zich aan het IoT [Plug and Play-schema voor georuimtelijke gegeven](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). Het telemetrie-schema van de huurauto ziet er als volgt uit:

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

Laten we apparaattelemetrie aan boord gebruiken om ons doel te bereiken. We willen geofencingregels uitvoeren. En we willen reageren wanneer we een gebeurtenis ontvangen die aangeeft dat de auto is verplaatst. Om dit te doen, abonneren we ons op de apparaattelemetrie-evenementen van IoT Hub via Event Grid. 

Er zijn verschillende manieren om je te abonneren op Event Grid, in deze tutorial gebruiken we Azure Functions. Azure Functions reageert op gebeurtenissen die zijn gepubliceerd in het Event Grid. Het implementeert ook de bedrijfslogica van autoverhuur, die is gebaseerd op ruimtelijke analyses van Azure Maps. 

Code binnen Azure-functie controleert of het voertuig de geofence heeft verlaten. Als het voertuig de geofence heeft verlaten, verzamelt de Azure-functie aanvullende informatie, zoals het adres dat is gekoppeld aan de huidige locatie. De functie implementeert ook logica om betekenisvolle gebeurtenisgegevens op te slaan in een data blob-opslag die helpt bij het beschrijven van de gebeurtenisomstandigheden. 

De omstandigheden van het evenement kunnen nuttig zijn voor het autoverhuurbedrijf en de verhuurklant. Het volgende diagram geeft je een overzicht op hoog niveau van het systeem.

 
  <center>

  ![Systeemoverzicht](./media/tutorial-iot-hub-maps/system-diagram.png)
  
  </center>

De volgende afbeelding geeft het geofence-gebied weer dat blauw is gemarkeerd. De route van het huurvoertuig wordt aangegeven door een groene lijn.

  ![Geofence-route](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Vereisten 

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Om de stappen in deze zelfstudie te voltooien, moet je eerst een resourcegroep maken in de Azure-portal. Voer de volgende stappen uit om een resourcegroep te maken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Selecteer **Resourcegroepen**.
    
   ![Resourcegroepen](./media/tutorial-iot-hub-maps/resource-group.png)

3. Selecteer onder **Brongroepen** de optie **Toevoegen**.
    
   ![Brongroep toevoegen](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Voer de volgende eigenschapswaarden in:
    * **Abonnement:** Selecteer uw Azure-abonnement.
    * **Resourcegroep:** Voer "ContosoRental" in als de naam van de resourcegroep.
    * **Regio:** Selecteer een regio voor de resourcegroep.  

    ![Details van resourcegroep](./media/tutorial-iot-hub-maps/resource-details.png)

    Selecteer **Controleren en maken** en selecteer vervolgens **Maken**op de volgende pagina.

### <a name="create-an-azure-maps-account"></a>Een Azure Maps-account maken 

Om bedrijfslogica te implementeren op basis van ruimtelijke analyses van Azure Maps, moeten we een Azure Maps-account maken in de resourcegroep die we hebben gemaakt. Volg de instructies in [Een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) om een Azure Maps-accountabonnement met S1-prijsniveau te maken. Volg de stappen in [primaire sleutel ophalen](quick-demo-map-app.md#get-the-primary-key-for-your-account) om uw primaire sleutel voor uw account te verkrijgen. Zie [Verificatie beheren in Azure Maps](how-to-manage-authentication.md) voor meer informatie over verificatie in Azure Maps.



### <a name="create-a-storage-account"></a>Create a storage account

Om gebeurtenisgegevens te loggen, maken we een algemene **v2storage** die toegang biedt tot alle Azure Storage-services: blobs, bestanden, wachtrijen, tabellen en schijven.  We moeten dit opslagaccount in de resourcegroep "ContosoRental" plaatsen om gegevens als blobs op te slaan. Volg de instructies in [een opslagaccount maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) om een opslagaccount te maken. Vervolgens moeten we een container maken om blobs op te slaan. Volg hiervoor de onderstaande stappen:

1. Navigeer in jouw "opslagaccount - blob, bestand, tabel, wachtrij" naar Containers.

    ![blobs](./media/tutorial-iot-hub-maps/blobs.png)

2. Klik op de containerknop linksboven en noem jouw container "contoso-verhuur-logs" en klik op "OK".

    ![blob-container](./media/tutorial-iot-hub-maps/blob-container.png)

3. Navigeer naar de blade **Toegangssleutels** in jouw opslagaccount en kopieer de "naam opslagaccount" en "toegangssleutel". Deze zijn nodig in een latere stap.

    ![toegangssleutels](./media/tutorial-iot-hub-maps/access-keys.png)


Nu hebben we een opslagaccount en een container om gebeurtenisgegevens te loggen. Vervolgens maken we een IoT-hub.

### <a name="create-an-iot-hub"></a>Een IoT Hub maken

De IoT Hub is een beheerde service in de cloud. De IoT Hub fungeert als een centrale berichtenhub voor bidirectionele communicatie tussen een IoT-applicatie en de apparaten die ermee worden beheerd. Om apparaattelemetrie-berichten naar een Event Grid te routeren, maak je een IoT Hub binnen de "ContosoRental"-resourcegroep. Stel een berichtroute-integratie in waarbij we berichten filteren op basis van de motorstatus van de auto. We sturen ook telemetrie-berichten naar het Event Grid wanneer de auto rijdt.

> [!Note] 
> De functionaliteit van IoT Hub om telemetriegebeurtenissen van apparaten op Event Grid te publiceren, is in openbare preview. Openbare preview-functies zijn beschikbaar in alle regio's behalve **Oost-VS, West-VS, West-Europa, Azure Government, Azure China 21Vianet** en **Azure Germany**. 

Maak een Iot Hub door de stappen te volgen in [ een IoT Hub-sectie maken ](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).


### <a name="register-a-device"></a>Een apparaat registreren 

Om verbinding te maken met de IoT Hub, moet een apparaat worden geregistreerd. Volg de onderstaande stappen om een apparaat te registreren bij de IoT-hub:

1. Klik in uw IoT Hub op de blade "IoT-apparaten" en klik op "Nieuw".

    ![apparaat-toevoegen](./media/tutorial-iot-hub-maps/add-device.png)

2. Geef op de pagina voor het maken van een apparaat uw IoT-apparaat een naam en klik op 'Opslaan'.
    
    ![apparaat-register](./media/tutorial-iot-hub-maps/register-device.png)

3. Sla de **primaire verbindingsreeks** van jouw apparaat op om deze in een latere stap te gebruiken, waarin je met deze verbindingsreeks een tijdelijke aanduiding moet wijzigen.

    ![apparaat-toevoegen](./media/tutorial-iot-hub-maps/connection-string.png)

## <a name="upload-geofence"></a>Geofence uploaden

We gebruiken de [ Postman-app ](https://www.getpostman.com) om [ de geofence te uploaden ](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) naar de Azure Maps-service met behulp van de Azure Maps Data Upload API. Elke gebeurtenis waarbij de auto zich buiten deze geofence bevindt, wordt geregistreerd.

Open de Postman-app en volg de onderstaande stappen om de geofence te uploaden met behulp van de Azure Maps, Data Upload API.  

1. Klik in de Postman-app op nieuw | Maak een nieuwe en selecteer Aanvraag. Voer een Request-naam in voor Upload geofence data. Selecteer een verzameling of map waarin u deze wilt opslaan en klik op Save.

    ![Geofences uploaden met Postman](./media/tutorial-iot-hub-maps/postman-new.png)

2. Selecteer de methode HTTP POST op het tabblad Builder en voer de volgende URL in om een POST-aanvraag te doen.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    De waarde "geojson" naast de parameter `dataFormat` in het URL-pad vertegenwoordigt de indeling van de gegevens die worden geüpload.

3. Klik op **Params** en voer de volgende sleutel/waarde-paren in die voor de POST-aanvraag-URL moeten worden gebruikt. Vervang de waarde van de abonnementssleutel door uw Azure Maps-sleutel.
   
    ![Key-Value-parameters in Postman](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Klik op **Tekst**, selecteer vervolgens **onbewerkte** invoerindeling en kies **JSON (applicatie / tekst)** als invoerindeling in de vervolgkeuzelijst. Open het JSON-gegevensbestand [ hier ](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4) en kopieer de Json in het hoofdgedeelte als de gegevens om te uploaden en klik op **Verzenden**.
    
    ![gegevens posten](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Bekijk de antwoordkoppen. Bij een succesvol verzoek bevat de koptekst **Locatie** de status-URI om de huidige status van het uploadverzoek te controleren. De status-URI heeft de volgende indeling. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Kopieer jouw status-URI en voeg er een `subscription-key` -parameter aan toe. Wijs de waarde van de abonnementssleutel van uw Azure Maps-account toe aan de parameter `subscription-key`. De status-URI-indeling moet zijn zoals hieronder en `{Subscription-key}` vervangen door jouw abonnementssleutel.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Om dit te krijgen, `udId` open je een nieuw tabblad in de Postman-app en selecteer je GET HTTP-methode op het tabblad builder en dien je een GET-verzoek in bij de status-URI. Als je gegevens zijn geüpload, ontvang je een udId in de antwoordtekst. Kopieer de udId voor later gebruik.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


Vervolgens maken we een Azure-functie binnen de resourcegroep "ContosoRental" en stellen we vervolgens een berichtroute in IoT Hub in om telemetrieberichten van apparaten te filteren.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Maak een Azure-functie en voeg een Event Grid-abonnement toe

Azure Functions is een serverloze rekenservice waarmee we code on-demand kunnen uitvoeren, zonder dat we de rekeninfrastructuur expliciet moeten inrichten of beheren. Bekijk de documentatie over [ Azure-functies ](https://docs.microsoft.com/azure/azure-functions/functions-overview) voor meer informatie over Azure Functions. 

De logica die we in de functie implementeren, gebruikt de locatiegegevens die afkomstig zijn van de telemetrie van het apparaat in het voertuig om de geofence-status te beoordelen. Als een bepaald voertuig buiten de geofence komt, zal de functie meer informatie verzamelen, zoals het adres van de locatie, via de [ Get Search Address Reverse API ](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Deze API vertaalt een bepaalde locatiecoördinaat in een voor de mens begrijpelijk adres. 

Alle relevante evenementinformatie wordt vervolgens bewaard in de blob-winkel. Stap 5 hieronder verwijst naar de uitvoerbare code die een dergelijke logica implementeert. Volg de onderstaande stappen om een Azure-functie te maken die gegevenslogboeken naar de blob-container in het blob-opslagaccount verzendt en er een Event Grid-abonnement aan toevoegt.

1. Selecteer in het Azure Portal-dashboard een resource maken. Selecteer **Berekenen** in de lijst met beschikbare brontypen en selecteer vervolgens **Functie-app**.

    ![resource-maken](./media/tutorial-iot-hub-maps/create-resource.png)

2. Geef de functie-app een naam op de aanmaakpagina van **Functie-app**. Selecteer onder **Resourcegroep** de optie **Bestaande gebruiken** en selecteer "ContosoRental" in de vervolgkeuzelijst. Selecteer ".NET Core" als Runtime Stack. Selecteer onder **Hosting** voor **Opslagaccount** de naam van het opslagaccount uit een eerdere stap. In onze vorige stap noemden we het opslagaccount **v2storage**.  Selecteer vervolgens **Controleren + maken**.
    
    ![app-maken](./media/tutorial-iot-hub-maps/rental-app.png)

2. Bekijk de details van de functie-app en selecteer "Maken".

3. Zodra de app is gemaakt, moeten we er een functie aan toevoegen. Ga naar functie app. Klik op **Nieuwe functie** om een functie toe te voegen en kies **In-Portal** als ontwikkelomgeving. Selecteer vervolgens **Doorgaan**.

    ![functie-maken](./media/tutorial-iot-hub-maps/function.png)

4. Kies **Meer sjablonen** en klik op **Voltooien en sjablonen weergeven**. 

5. Selecteer de sjabloon met een **Azure Event Grid Trigger**. Installeer extensies als daarom wordt gevraagd, geef de functie een naam en selecteer **Maken**.

    ![functie-sjabloon](./media/tutorial-iot-hub-maps/eventgrid-funct.png)
    
    De **Azure Event Hub Trigger** en de **Azure Event Grid Trigger** hebben vergelijkbare pictogrammen. Zorg ervoor dat je de **Azure Event Grid Trigger** selecteert.

6. Kopieer de [C#-code](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) naar jouw functie.
 
7. Vervang in het C#-script de volgende parameters. Klik op **Opslaan**. Klik nog niet op **Uitvoeren**
    * Vervang de **SUBSCRIPTION_KEY** door de primaire abonnementssleutel van je Azure Maps-account.
    * Vervang de **UDID** door de udId van de geofence die je hebt geüpload, 
    * De functie **CreateBlobAsync** in het script maakt een blob per gebeurtenis in het gegevensopslagaccount. Vervang de **ACCESS_KEY**, **ACCOUNT_NAME** en **STORAGE_CONTAINER_NAME** door de toegangssleutel, accountnaam en gegevensopslagcontainer van je opslagaccount.

10. Klik op **Event Grid-abonnement toevoegen**.
    
    ![event-grid-toevoegen](./media/tutorial-iot-hub-maps/add-egs.png)

11. Vul abonnementsgegevens in, geef onder **EVENT-ABONNEMENTSGEGEVENS** je event-abonnement op. Kies voor Event Schema "Event Grid Schema". Selecteer onder **ONDERWERPGEGEVENS** "Azure IoT Hub-accounts" als onderwerptype. Kies hetzelfde abonnement dat JE hebt gebruikt voor het maken van de resourcegroep, selecteer "ContosoRental" als "Resourcegroep". Kies de IoT Hub die je hebt gemaakt als een "bron". Kies **Telemetrie van apparaat** als gebeurtenistype. Nadat je deze opties heeft gekozen, zie je automatisch het "Onderwerptype" veranderen in "IoT Hub".

    ![event-grid-abonnement](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>De gebeurtenissen filteren met behulp van berichtroutering van IoT Hub

Nadat je een Event Grid-abonnement aan de Azure-functie hebt toegevoegd, zie je een standaardberichtroute naar Event Grid in de blade **Message Routing** van IoT Hub. Met berichtroutering kun je verschillende gegevenstypen naar verschillende eindpunten routeren. Je kunt bijvoorbeeld telemetrieberichten van apparaten, levenscyclusgebeurtenissen van apparaten en dubbele wijzigingen van apparaten routeren. Voor meer informatie over IoT Hub-berichtenroutering ga je naar [IoT Hub-berichtroutering gebruiken](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

![hub-EG-route](./media/tutorial-iot-hub-maps/hub-route.png)

In ons voorbeeldscenario willen we alle berichten filteren waarin het huurvoertuig rijdt. Om dergelijke apparaattelemetriegebeurtenissen naar Event Grid te publiceren, gebruiken we de routeringsquery om de gebeurtenissen te filteren waarbij de `Engine` eigenschap **"ON"** is. Er zijn verschillende manieren om IoT-apparaat-naar-cloud-berichten te doorzoeken. Voor meer informatie over de syntaxis van berichtroutering ga je naar [IoT Hub-berichtroutering](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax). Als je een routeringsquery wilt maken, klik je op de **RouteToEventGrid**-route, vervang je de **Routing-query** door **"Engine = 'ON'"** en klik je op **Opslaan**. Nu zal de IoT-hub alleen apparaattelemetrie publiceren als de engine is ingeschakeld.

![hub-EG-filter](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Stuur telemetriegegevens naar IoT Hub

Zodra onze Azure-functie actief is, kunnen we telemetriegegevens verzenden naar de IoT Hub, die deze naar het Event Grid zal leiden. Laten we een C#-applicatie gebruiken om locatiegegevens te simuleren voor een in-voertuig apparaat van een huurauto. Om de applicatie uit te voeren, heb je .NET Core SDK 2.1.0 of hoger nodig op je ontwikkelingsmachine. Volg de onderstaande stappen om gesimuleerde telemetriegegevens naar IoT Hub te verzenden.

1. Download het C#-project [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation). 

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

  ![Terminal uitvoer](./media/tutorial-iot-hub-maps/terminal.png)

Als je nu de blob-opslagcontainer opent, zou je vier blobs moeten kunnen zien voor locaties waar het voertuig zich buiten de geofence bevond.

![Blob invoeren](./media/tutorial-iot-hub-maps/blob.png)

De onderstaande kaart toont vier punten waar het voertuig zich buiten de geofence bevond, geregistreerd op regelmatige tijdsintervallen.

![schending kaart](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het verkennen van Azure Maps API's die in deze zelfstudie worden gebruikt, ga naar:

* [Zoekadres omkeren](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Geofence downloaden](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Voor een volledige lijst van Azure Maps REST API's, bekijk:

* [Azure Maps REST API's](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Voor meer informatie over IoT Plug and Play, bekijk:

* [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp)

Voor een lijst met apparaten die Azure-gecertificeerd zijn voor IoT ga je naar:

* [Azure-gecertificeerde apparaten](https://catalog.azureiotsolutions.com/)

Voor meer informatie over het verzenden van apparaat naar cloud-telemetrie en omgekeerd, bekijk:

* [Telemetrie verzenden vanaf een apparaat](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

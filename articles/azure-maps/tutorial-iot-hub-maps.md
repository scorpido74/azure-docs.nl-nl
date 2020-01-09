---
title: 'Zelf studie: een IoT-ruimtelijke analyse implementeren met behulp van Azure Maps'
description: "Zelf studie: IoT Hub integreren met Azure Maps-service-Api's."
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 6c35b52149e3c0117c727771d38d0f010180fc63
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432814"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Zelf studie: een IoT-ruimtelijke analyse implementeren met behulp van Azure Maps

Het bijhouden en vastleggen van relevante gebeurtenissen die plaatsvinden in ruimte en tijd is een veelvoorkomend IoT-scenario. Bijvoorbeeld in vloot beheer, tracering van middelen, mobiliteit en Smart City-toepassingen. In deze zelf studie wordt u begeleid bij het maken van een oplossings patroon voor het gebruik van Azure Maps-Api's op basis van relevante gebeurtenissen die door IoT Hub worden vastgelegd, met behulp Event Grid van het gebeurtenis abonnements model dat

In deze zelf studie doet u het volgende:

> [!div class="checklist"]
> * Een IoT Hub maken.
> * Een geofence-gebied uploaden in de gegevensservices van Azure Maps met behulp van de Data Upload-API.
> * Een functie maken in Azure Functions, bedrijfs logica implementeren op basis van Azure Maps ruimtelijke analyse.
> * Abonneer u op IoT Device-telemetrie-gebeurtenissen vanuit de Azure-functie via Event Grid.
> * De telemetrie-gebeurtenissen filteren met behulp van IoT Hub bericht routering.
> * Maak een opslag account om relevante gebeurtenis gegevens op te slaan.
> * Een IoT-apparaat in het Voer tuig simuleren.
    

## <a name="use-case"></a>Toepassing

We exemplifyen de oplossing voor een scenario waarbij een auto verhuur bedrijf voornemens is om gebeurtenissen te controleren en te registreren voor de huur auto's. Doorgaans huren bedrijven huurden auto's voor een specifieke geografische regio en moeten ze hun verblijfs gegevens bijhouden terwijl ze worden gehuurd. Elke instantie, waarbij een auto wordt betrokken die de aangewezen geografische regio verlaat, moet worden geregistreerd zodat beleids regels, kosten en andere zakelijke aspecten op de juiste wijze kunnen worden verwerkt.

In onze use-case zijn de huur auto's uitgerust met IoT-apparaten die regel matig telemetrie-gegevens verzenden naar Azure IoT Hub. De telemetrie bevat de huidige locatie en geeft aan of de engine van de auto actief is. Het locatie schema van het apparaat voldoet aan het IoT [Plug en Play-schema voor georuimtelijke gegevens](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md). Het telemetrie-schema van het huur auto ziet er als volgt uit:

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

De telemetrie van het apparaat in het Voer tuig kan worden gebruikt om het doel te bereiken. Het doel is om de geoomheinings regels uit te voeren en op de juiste wijze op te volgen wanneer een gebeurtenis die aangeeft dat de auto is verplaatst, is ontvangen. Om dit te doen, zullen we abonneren op de telemetrie gebeurtenissen van het apparaat van IoT Hub via Event Grid, zodat de gewenste zakelijke logica van de klant alleen kan worden uitgevoerd als dat nodig is. Er zijn verschillende manieren om u te abonneren op Event Grid. in deze zelf studie gaan we gebruikmaken van Azure Functions. De Azure Functions reageert op gebeurtenissen die zijn gepubliceerd in Event Grid en implementeert de bedrijfs logica voor auto verhuur op basis van Azure Maps ruimtelijke analyse. De functie Azure bestaat uit het controleren of het Voer tuig de geofence heeft verlaten en, als dit het geval is, het verzamelen van aanvullende gegevens, zoals het adres dat is gekoppeld aan de huidige locatie. De functie implementeert ook logica voor het opslaan van belang rijke gebeurtenis gegevens in een gegevensblob-opslag waarmee u nauw keurige beschrijving van de gebeurtenis omstandigheden kunt bieden aan de analist van de auto verhuur en de huur klant.

In het volgende diagram ziet u een overzicht op hoog niveau van het systeem.

 
  <center>

  overzicht van ![systeem](./media/tutorial-iot-hub-maps/system-diagram.png)</center>

In de afbeelding hieronder ziet u dat het geofence-gebied is gemarkeerd in de route van het blauwe en het huur voertuig als een groene lijn.

  ![Geofence-route](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Vereisten 

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Als u de stappen in deze zelf studie wilt uitvoeren, moet u eerst een resource groep maken in de Azure Portal. Volg de onderstaande stappen om een resource groep te maken:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer **resource groepen**.
    
   ![Resourcegroepen](./media/tutorial-iot-hub-maps/resource-group.png)

3. Selecteer onder resource groepen de optie **toevoegen**.
    
   ![Resource groep toevoegen](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Voer de volgende eigenschaps waarden in:
    * **Abonnement:** Selecteer een Azure-abonnement.
    * **Resource groep:** Geef ' ContosoRental ' op als de naam van de resource groep.
    * **Regio:** Selecteer een regio voor de resource groep.  

    ![Details van resource groep](./media/tutorial-iot-hub-maps/resource-details.png)

    Klik op **controleren + maken** en kies **maken** op de volgende pagina.

### <a name="create-an-azure-maps-account"></a>Een Azure Maps-account maken 

Voor het implementeren van bedrijfs logica op basis van Azure Maps ruimtelijke analyse moet er een Azure Maps-account worden gemaakt in de resource groep die we hebben gemaakt. Volg de instructies in [een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) om een abonnement voor een Azure Maps account te maken met de prijs categorie S1 en volg de stappen in [primaire sleutel ophalen](quick-demo-map-app.md#get-the-primary-key-for-your-account) om de primaire sleutel voor uw account op te halen. Zie [verificatie beheren in azure Maps](how-to-manage-authentication.md)voor meer informatie over verificatie in azure Maps.



### <a name="create-a-storage-account"></a>Maak een opslagaccount

Om gebeurtenis gegevens vast te leggen, maken we een **v2storage** -account voor algemeen gebruik in de resource groep ' ContosoRental ' om gegevens als blobs op te slaan. Als u een opslag account wilt maken, volgt u de instructies in [een opslag account maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal). Vervolgens moet u een container maken om blobs op te slaan. Volg de onderstaande stappen om dit te doen:

1. Navigeer in uw opslag account naar blobs.

    ![blobs](./media/tutorial-iot-hub-maps/blobs.png)

2. Klik op de knop container linksboven en noem uw container "Contoso-verhuur-logs" en klik op OK.

    ![BLOB-container](./media/tutorial-iot-hub-maps/blob-container.png)

3. Ga naar de Blade **toegangs sleutels** in uw opslag account en kopieer de account naam en de toegangs sleutel. deze worden later gebruikt.

    ![Access-sleutels](./media/tutorial-iot-hub-maps/access-keys.png)


Nu we een opslag account en een container hebben om gebeurtenis gegevens te registreren, gaan we een IoT-hub maken.

### <a name="create-an-iot-hub"></a>Een IoT Hub maken

Een IoT Hub is een beheerde service in de cloud die fungeert als een centrale Message hub voor bidirectionele communicatie tussen een IoT-toepassing en de apparaten die door IT worden beheerd. Als u telemetrie-berichten wilt routeren naar een Event Grid, wordt er een IoT Hub gemaakt in de resource groep ' ContosoRental ' en wordt een bericht route-integratie ingesteld, waarbij we berichten filteren op basis van de engine status van de auto en de telemetrie van het apparaat verzenden berichten naar de Event Grid wanneer de auto wordt verplaatst. 

> [!Note] 
> De functionaliteit van IoT Hub voor het publiceren van telemetrie-faxgebeurtenissen op Event Grid is in open bare preview. Open bare preview-functies zijn beschikbaar in alle regio's behalve **VS-Oost, VS-West, Europa-West, Azure Government, Azure China 21vianet** en **Azure Duitsland**. 

Maak een IOT-hub door de stappen in het [gedeelte een IOT hub maken](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub)te volgen.


### <a name="register-a-device"></a>Een apparaat registreren 

Als u verbinding wilt maken met de IoT Hub, moet u een apparaat registreren. Volg de onderstaande stappen om een apparaat te registreren met IoT hub:

1. Klik in uw IoT Hub op de Blade IoT-apparaten en klik op nieuw.

    ![toevoegen-apparaat](./media/tutorial-iot-hub-maps/add-device.png)

2. Geef op de pagina een apparaat maken de naam uw IoT-apparaat op en klik op opslaan.
    
    ![REGI ster-apparaat](./media/tutorial-iot-hub-maps/register-device.png)


## <a name="upload-geofence"></a>Geofence uploaden

We gebruiken de [postman-toepassing](https://www.getpostman.com) om [de geofence te uploaden](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) naar de Azure Maps-service met behulp van de Azure Maps Data upload-API. Elke gebeurtenis wanneer de auto zich buiten deze geofence bevindt, wordt geregistreerd.

Open de Postman-app en voer de onderstaande stappen uit om de geofence te uploaden met behulp van de Azure Maps Data upload-API.  

1. Klik in de app postman op New | Nieuwe maken en aanvraag selecteren. Voer een Request-naam in voor Upload geofence data. Selecteer een verzameling of map waarin u deze wilt opslaan en klik op Save.

    ![Geofences uploaden met Postman](./media/tutorial-iot-hub-maps/postman-new.png)

2. Selecteer de methode HTTP POST op het tabblad Builder en voer de volgende URL in om een POST-aanvraag te doen.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    De ' geojson '-waarde voor de para meter `dataFormat` in het URL-pad vertegenwoordigt de indeling van de gegevens die worden geüpload.

3. Klik op **Params** en voer de volgende sleutel/waarde-paren in die voor de POST-aanvraag-URL moeten worden gebruikt. Vervang de abonnee sleutel waarde door uw Azure Maps sleutel.
   
    ![Key-Value-parameters in Postman](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Klik op **Body** , selecteer **onbewerkte** invoer indeling en kies **JSON (toepassing/tekst)** als invoer indeling in de vervolg keuzelijst. Open het JSON-gegevens bestand [hier](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)en kopieer de JSON naar het gedeelte hoofd tekst in het bericht postman als de gegevens die moeten worden geüpload en klik op **verzenden**.
    
    ![Post gegevens](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Controleer de antwoord headers. Bij een geslaagde aanvraag bevat de **locatie** header de status-URI om de huidige status van de upload aanvraag te controleren. De status-URI heeft de volgende indeling. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Kopieer uw status-URI en voeg een `subscription-key` para meter toe met de waarde van de abonnements sleutel van uw Azure Maps-account. De URI-indeling voor de status moet er als volgt uitzien:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Als u de wilt `udId`, opent u een nieuw tabblad in de app postman en selecteert u HTTP-methode ophalen op het tabblad opbouw functie en maakt u een GET-aanvraag op de status-URI. Als het uploaden van uw gegevens is geslaagd, ontvangt u een udId in de antwoord tekst. Kopieer de udId voor later gebruik.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


Vervolgens maakt u een Azure-functie in de resource groep ' ContosoRental ' en stelt u vervolgens een bericht route in IoT Hub om telemetrie-berichten van apparaten te filteren.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Een Azure-functie maken en een Event Grid-abonnement toevoegen

Azure Functions is een serverloze compute-service waarmee u code op aanvraag kunt uitvoeren zonder dat u de reken infrastructuur expliciet hoeft in te richten of te beheren. Raadpleeg de documentatie van [Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) voor meer informatie over Azure functions. De logica die we in de functie implementeren, maakt gebruik van de locatie gegevens die afkomstig zijn van de telemetrie van het apparaat in het Voer tuig voor het beoordelen van de geofence-status. Als een bepaald voer tuig buiten de geofence weggaat, verzamelt de functie vervolgens meer informatie, zoals het adres van de locatie via [Zoek adres terugdraaiende API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) die een bepaalde locatie vertaalt in een adres dat mensen begrijpt. Alle relevante gebeurtenis gegevens worden vervolgens opgeslagen in de Blob-opslag. Stap 5 hieronder verwijst naar de uitvoer bare code die deze logica implementeert. Volg de onderstaande stappen om een Azure-functie te maken die gegevens logboeken naar de BLOB-container in het opslag account verzendt en een Event Grid-abonnement toevoegt.

1. Selecteer in het dash board van Azure Portal een resource maken. Selecteer **berekenen** in de lijst met beschik bare resource typen en selecteer vervolgens **functie-app**.

    ![maken-resource](./media/tutorial-iot-hub-maps/create-resource.png)

2. Geef op de pagina functie-app maken een naam op voor de functie-app, onder **resource groep** Selecteer **bestaande gebruiken**en selecteer ' ContosoRental ' in de vervolg keuzelijst. Selecteer .NET Core als runtime stack, selecteer **bestaande gebruiken** en **Selecteer '** contosorentaldata ' in de vervolg keuzelijst en klik op **maken**.
    
    ![maken-app](./media/tutorial-iot-hub-maps/rental-app.png)

3. Zodra de app is gemaakt, moet er een functie aan worden toegevoegd. Ga naar de functie-app en klik op **nieuwe functie** om een functie toe te voegen, kies **in-portal** als ontwikkel omgeving en selecteer **door gaan**.

    ![maken-functie](./media/tutorial-iot-hub-maps/function.png)

4. Kies **meer sjablonen** en klik op **volt ooien en sjablonen weer geven**. 

5. Selecteer de sjabloon met een **Azure Event grid trigger**. Installeer uitbrei dingen als u hierom wordt gevraagd, noem de functie en druk op **maken**.

    ![functie-sjabloon](./media/tutorial-iot-hub-maps/eventgrid-funct.png)

6. Kopieer de [c#-code](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) naar uw functie en klik op **Opslaan**.
 
7. Vervang in het c#-script de volgende para meters:
    * Vervang de **SUBSCRIPTION_KEY** door de primaire abonnements sleutel van uw Azure Maps-account.
    * Vervang de **UDID** door de UDID van de geofence die u hebt geüpload, 
    * Met de functie **CreateBlobAsync** in het script maakt u een BLOB per gebeurtenis in het account voor gegevens opslag. Vervang de **ACCESS_KEY**, **ACCOUNT_NAME** en **STORAGE_CONTAINER_NAME** met de toegangs sleutel van uw opslag account en de account naam en de gegevens opslag container.

10. Klik op **Event grid abonnement toevoegen**.
    
    ![Add-Event-grid](./media/tutorial-iot-hub-maps/add-egs.png)

11. Vul de abonnements gegevens in, onder DETAILS van het **gebeurtenis abonnement** naam van uw abonnement en kies voor het gebeurtenis schema Event grid schema. Selecteer onder **Details van onderwerp** ' Azure IOT hub-accounts ' als onderwerp type, Kies hetzelfde abonnement dat u hebt gebruikt voor het maken van de resource groep, selecteer ' ContosoRental ' als de resource groep en kies de IOT hub die u hebt gemaakt als een resource. Kies **apparaat-telemetrie** als gebeurtenis type. Nadat u deze opties hebt gekozen, ziet u dat het onderwerp type wordt gewijzigd in automatisch IoT Hub.

    ![Event-grid-abonnement](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Gebeurtenissen filteren met behulp van IoT Hub bericht routering

Nadat u een Event Grid-abonnement aan de Azure-functie hebt toegevoegd, kunt u nu een standaard route voor berichten weer geven naar Event Grid in de Blade **bericht routering** van IOT hub. Met bericht routering kunt u verschillende gegevens typen routeren, zoals telemetrie-berichten van apparaten, gebeurtenissen in de levens cyclus van een apparaat en gebeurtenissen met een dubbele wijziging van het apparaat naar verschillende eind punten. Zie [IOT hub bericht routering gebruiken](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)voor meer informatie over IOT hub-bericht routering.

![hub-BV-route](./media/tutorial-iot-hub-maps/hub-route.png)

In het voorbeeld scenario willen we alle berichten filteren waarbij het huur voertuig wordt verplaatst. Als u dergelijke telemetrie-gebeurtenissen naar Event Grid wilt publiceren, zullen we de routerings query gebruiken om de gebeurtenissen te filteren waarbij de eigenschap `Engine` is **ingeschakeld**. Er zijn verschillende manieren om een query uit te sturen van IoT-apparaat-naar-Cloud-berichten, Zie [IOT hub Message Routing](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax)(Engelstalig) voor meer informatie over de routerings syntaxis van berichten. Als u een routerings query wilt maken, klikt u op de **RouteToEventGrid** -route en vervangt u de **routerings query** door **' Engine = ' op ' '** en klikt u op **Opslaan**. IoT hub publiceert nu alleen telemetrie van apparaten waarop de engine zich bevindt.

![hub-bijvoorbeeld-filter](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Telemetriegegevens naar IoT Hub verzenden

Zodra de Azure function actief is, worden er telemetriegegevens verzonden naar de IoT Hub, die deze routert naar de Event Grid. We gebruiken een c#-toepassing voor het simuleren van locatie gegevens voor een apparaat in het Voer tuig van een huur auto. Als u de toepassing wilt uitvoeren, hebt u de .NET Core SDK 2.1.0 of hoger nodig op uw ontwikkel computer. Volg de onderstaande stappen om gesimuleerde telemetriegegevens naar IoT Hub te verzenden.

1. Down load het [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) c#-project. 

2. Open het simulatedCar.cs-bestand in een tekst editor naar keuze en vervang de waarde van de `connectionString` door het account dat u hebt opgeslagen toen u het apparaat registreerde en sla de wijzigingen in het bestand op.
 
3. Navigeer in het lokale terminal venster naar de hoofdmap van het C# project en voer de volgende opdracht uit om de vereiste pakketten te installeren voor de toepassing met gesimuleerde apparaten:
    
    ```cmd/sh
    dotnet restore
    ```

4. Voer in dezelfde Terminal de volgende opdracht uit om de simulatie toepassing voor de huur auto te bouwen en uit te voeren:

    ```cmd/sh
    dotnet run
    ```

  Uw lokale terminal moet er als volgt uitzien.

  ![Terminal uitvoer](./media/tutorial-iot-hub-maps/terminal.png)

Als u de BLOB storage-container nu opent, kunt u vier blobs zien voor locaties waar het Voer tuig zich buiten de geofence bevindt.

![BLOB invoeren](./media/tutorial-iot-hub-maps/blob.png)

De onderstaande kaart toont vier punten waar het Voer tuig zich buiten de geofence bevindt, vastgelegd op regel matige tijdstippen.

![fout toewijzing](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie Azure Maps Api's die in deze zelf studie worden gebruikt:

* [Zoek adres terugdraaien](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Geofence ophalen](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Zie voor een volledige lijst met Azure Maps REST-Api's:

* [Azure Maps REST-Api's](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Zie voor meer informatie over IoT Plug en Play:

* [IoT-Plug en Play](https://docs.microsoft.com/azure/iot-pnp)

Ga voor een lijst met apparaten die Azure Certified voor IoT zijn naar:

* [Azure Certified-apparaten](https://catalog.azureiotsolutions.com/)

Zie voor meer informatie over het verzenden van een apparaat naar een Cloud-telemetrie en omgekeerd:

* [Telemetrie verzenden vanaf een apparaat](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

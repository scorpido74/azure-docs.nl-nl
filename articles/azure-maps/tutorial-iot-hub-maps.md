---
title: 'Zelfstudie: IoT-ruimtelijke analyse implementeren | Microsoft Azure Maps'
description: Integreer IoT Hub met Microsoft Azure Maps-service API's.
author: philmea
ms.author: philmea
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: cfea9aa7bfcc9a9698bb93bdf54797481b8539ce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333961"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Zelfstudie: IoT-ruimtelijke analyses implementeren met Azure Maps

In een IoT-scenario is het gebruikelijk om relevante gebeurtenissen vast te leggen en bij te houden die zich in ruimte en tijd voordoen. Voorbeelden van scenario's zijn fleet management, asset tracking, mobiliteit en smart city applicaties. Deze zelfstudie leidt u door een oplossingspatroon met behulp van de Azure Maps API's. Relevante gebeurtenissen worden vastgelegd door IoT Hub, met behulp van het abonnementsmodel voor evenementen dat wordt geleverd door het gebeurtenisraster.

In deze tutorial zul je:

> [!div class="checklist"]
> * Een IoT Hub maken.
> * Een geofence-gebied uploaden in de gegevensservices van Azure Maps met behulp van de Data Upload-API.
> * Maak een functie in Azure-functies en implementeer bedrijfslogica op basis van ruimtelijke analyses van Azure Maps.
> * Abonneer u op Telemetriegebeurtenissen van IoT-apparaten vanuit de Azure-functie via gebeurtenisraster.
> * De telemetriegebeurtenissen filteren met behulp van IoT Hub-berichtroutering.
> * Maak een opslagaccount aan om relevante gebeurtenisgegevens op te slaan.
> * Simuleer een IoT-apparaat in het voertuig.
    

## <a name="use-case"></a>Gebruiksvoorbeeld

Deze oplossing toont een scenario waarin een autoverhuurbedrijf van plan is om evenementen voor zijn huurauto's te monitoren en te registreren. Autoverhuurbedrijven huren meestal auto's naar een specifieke geografische regio. Ze moeten de auto's volgen terwijl ze worden gehuurd. Exemplaren van een auto die de gekozen geografische regio verlaat, moeten worden geregistreerd. Het registreren van gegevens zorgt ervoor dat beleid, kosten en andere zakelijke aspecten correct worden behandeld.

In onze use case zijn de huurauto's uitgerust met IoT-apparaten die regelmatig telemetriegegevens naar Azure IoT Hub verzenden. De telemetrie omvat de huidige locatie en geeft aan of de motor van de auto draait. Het locatieschema van het apparaat houdt zich aan het IoT [Plug and Play-schema voor georuimtelijke gegevens.](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md) Het telemetrieschema van de huurauto ziet eruit als:

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

Laten we telemetrie in het voertuig gebruiken om ons doel te bereiken. We willen geofencing regels uitvoeren. En we willen reageren wanneer we een gebeurtenis ontvangen die aangeeft dat de auto is verplaatst. Hiervoor abonneren we ons op de telemetriegebeurtenissen van het apparaat van IoT Hub via Event Grid. 

Er zijn verschillende manieren om je te abonneren op Event Grid, in deze zelfstudie gebruiken we Azure Functions. Azure Functions reageert op gebeurtenissen die zijn gepubliceerd in het gebeurtenisraster. Het implementeert ook de logica van autoverhuurbedrijven, die is gebaseerd op ruimtelijke analyse van Azure Maps. 

Code in de Azure-functie controleert of het voertuig de geofence heeft verlaten. Als het voertuig de geofence heeft verlaten, verzamelt de Azure-functie aanvullende informatie, zoals het adres dat is gekoppeld aan de huidige locatie. De functie implementeert ook logica om zinvolle gebeurtenisgegevens op te slaan in een opslag van een gegevensblob die helpt bij het geven van een beschrijving van de gebeurtenisomstandigheden. 

De omstandigheden van het evenement kunnen nuttig zijn voor het autoverhuurbedrijf en de verhuurklant. Het volgende diagram geeft u een overzicht op hoog niveau van het systeem.

 
  <center>

  ![Systeemoverzicht](./media/tutorial-iot-hub-maps/system-diagram.png)
  
  </center>

De volgende figuur vertegenwoordigt het geofence-gebied dat in het blauw is gemarkeerd. De route van het huurvoertuig wordt aangegeven door een groene lijn.

  ![Geofence route](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Vereisten 

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Als u de stappen in deze zelfstudie wilt voltooien, moet u eerst een brongroep maken in de Azure-portal. Ga als volgt te werk om een resourcegroep te maken:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer **Resourcegroepen**.
    
   ![Resourcegroepen](./media/tutorial-iot-hub-maps/resource-group.png)

3. Selecteer **Onder Resourcegroepen**de optie **Toevoegen**.
    
   ![Resourcegroep toevoegen](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Voer de volgende eigenschapswaarden in:
    * **Abonnement:** Selecteer uw Azure-abonnement.
    * **Resourcegroep:** Voer 'ContosoRental' in als naam van de resourcegroep.
    * **Regio:** Selecteer een gebied voor de resourcegroep.  

    ![Resourcegroepdetails](./media/tutorial-iot-hub-maps/resource-details.png)

    Selecteer **Controleren + maken**en selecteer Vervolgens **Maken** op de volgende pagina.

### <a name="create-an-azure-maps-account"></a>Een Azure Maps-account maken 

Als u bedrijfslogica wilt implementeren op basis van ruimtelijke analyses van Azure Maps, moeten we een Azure Maps-account maken in de brongroep die we hebben gemaakt. Volg instructies in [Een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) om een Azure Maps-accountabonnement te maken met s1-prijscategorie. Volg de stappen in [de primaire sleutel](quick-demo-map-app.md#get-the-primary-key-for-your-account) om uw primaire sleutel voor uw account te verkrijgen. Zie Verificatie beheren in Azure [Maps voor](how-to-manage-authentication.md)meer informatie over verificatie in Azure Maps.



### <a name="create-a-storage-account"></a>Een opslagaccount maken

Als u gebeurtenisgegevens wilt registreren, maken we een **v2-opslag** voor algemene doeleinden die toegang biedt tot alle Azure Storage-services: blobs, bestanden, wachtrijen, tabellen en schijven.  We moeten dit opslagaccount plaatsen in de resourcegroep 'ContosoRental' om gegevens op te slaan als blobs. Als u een opslagaccount wilt maken, volgt u de instructie bij [het maken van een opslagaccount.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) Vervolgens moeten we een container maken om blobs op te slaan. Volg de onderstaande stappen om dit te doen:

1. Navigeer in uw 'opslagaccount - blob, bestand, tabel, wachtrij' naar Containers.

    ![Blobs](./media/tutorial-iot-hub-maps/blobs.png)

2. Klik op de containerknop linksboven en geef de naam van uw container "contoso-rental-logs" en klik op 'OK'.

    ![blob-container](./media/tutorial-iot-hub-maps/blob-container.png)

3. Navigeer naar het blade **van toegangssleutels** in uw opslagaccount en kopieer de naam 'opslagaccount' en 'toegangssleutel'. Ze zijn nodig in een latere stap.

    ![toegangstoetsen](./media/tutorial-iot-hub-maps/access-keys.png)


Nu hebben we een opslagaccount en een container om gebeurtenisgegevens in te loggen. Vervolgens maken we een IoT-hub.

### <a name="create-an-iot-hub"></a>Een IoT Hub maken

De IoT Hub is een managed service in de cloud. De IoT Hub fungeert als een centrale berichtenhub voor bidirectionele communicatie tussen een IoT-toepassing en de apparaten die ermee worden beheerd. Als u telemetrieberichten van apparaten wilt routeren naar een gebeurtenisraster, maakt u een IoT-hub binnen de resourcegroep 'ContosoRental'. Stel een berichtroute-integratie in waar we berichten filteren op basis van de motorstatus van de auto. We sturen ook telemetrieberichten van apparaten naar het Gebeurtenisraster wanneer de auto in beweging is.

> [!Note] 
> De functionaliteit van IoT Hub voor het publiceren van apparaattelemetriegebeurtenissen op gebeurtenisraster staat in de openbare preview. Openbare preview-functies zijn beschikbaar in alle regio's **behalve Oost-VS, West-VS, West-Europa, Azure Government, Azure China 21Vianet** en **Azure Germany**. 

Maak een Iot-hub door de stappen te volgen in [het maken van een IoT Hub-sectie.](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub)


### <a name="register-a-device"></a>Een apparaat registreren 

Om verbinding te maken met de IoT Hub moet een apparaat worden geregistreerd. Voer de onderstaande stappen uit om een apparaat te registreren met de IoT-hub:

1. Klik in uw IoT-hub op het mes "IoT-apparaten" en klik op 'Nieuw'.

    ![invoegapparaat](./media/tutorial-iot-hub-maps/add-device.png)

2. Geef op de pagina Een apparaat maken een naam aan uw IoT-apparaat en klik op Opslaan.
    
    ![register-apparaat](./media/tutorial-iot-hub-maps/register-device.png)

3. Sla de **tekenreeks Primaire verbinding** van uw apparaat op om deze in een latere stap te gebruiken, waarbij u een tijdelijke aanduiding met deze verbindingstekenreeks moet wijzigen.

    ![invoegapparaat](./media/tutorial-iot-hub-maps/connection-string.png)

## <a name="upload-geofence"></a>Geofence uploaden

We gebruiken de [toepassing Postman](https://www.getpostman.com) om [de geofence](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) te uploaden naar de Azure Maps-service met behulp van de Azure Maps Data Upload API. Elke gebeurtenis wanneer de auto zich buiten deze geofence bevindt, wordt geregistreerd.

Open de Postman-app en volg de onderstaande stappen om de geofence te uploaden met behulp van de Azure Maps, Data Upload API.  

1. Klik in de Postbode-app op nieuw | Maak nieuw en selecteer Aanvraag. Voer een Request-naam in voor Upload geofence data. Selecteer een verzameling of map waarin u deze wilt opslaan en klik op Save.

    ![Geofences uploaden met Postman](./media/tutorial-iot-hub-maps/postman-new.png)

2. Selecteer de methode HTTP POST op het tabblad Builder en voer de volgende URL in om een POST-aanvraag te doen.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    De waarde 'geojson' `dataFormat` ten opzichte van de parameter in het URL-pad vertegenwoordigt de indeling van de gegevens die worden geüpload.

3. Klik op **Params** en voer de volgende sleutel/waarde-paren in die voor de POST-aanvraag-URL moeten worden gebruikt. Vervang de waarde van abonnementssleutel door uw Azure Maps-sleutel.
   
    ![Key-Value-parameters in Postman](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Klik **op Hoofdtekst** en selecteer **vervolgens de ruwe** invoerindeling en kies **JSON (toepassing/tekst)** als invoerindeling in de vervolgkeuzelijst. Open [hier](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)het JSON-gegevensbestand en kopieer de Json in de hoofdsectie als de gegevens die u wilt uploaden en klik op **Verzenden**.
    
    ![postgegevens](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Bekijk de antwoordkoppen. Op een geslaagd verzoek bevat de **koplocatie** de status URI om de huidige status van de uploadaanvraag te controleren. De status URI is van de volgende indeling. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Kopieer uw status URI `subscription-key` en een parameter toe te passen. Wijs de waarde van uw Azure `subscription-key` Maps-accountabonnementssleutel toe aan de parameter. De status URI-indeling moet zijn `{Subscription-key}` zoals hieronder, en vervangen door uw abonnementssleutel.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Als u `udId` de app wilt downloaden, opent u een nieuw tabblad in de Postman-app en selecteert u DE HTTP-methode DOWNLOADEN op het tabblad Bouwer en doet u een GET-verzoek bij de status URI. Als het uploaden van uw gegevens succesvol is, ontvangt u een udId in de reactieinstantie. Kopieer de udId voor later gebruik.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


Vervolgens maken we een Azure-functie binnen de resourcegroep 'ContosoRental' en stellen we vervolgens een berichtroute in IoT Hub in om telemetrieberichten van apparaten te filteren.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Een Azure-functie maken en een gebeurtenisrasterabonnement toevoegen

Azure Functions is een serverloze compute service waarmee we code on-demand kunnen uitvoeren, zonder dat we de compute-infrastructuur expliciet hoeven in te richten of te beheren. Voor meer informatie over Azure-functies, bekijk de [azure-functiesdocumentatie.](https://docs.microsoft.com/azure/azure-functions/functions-overview) 

De logica die we implementeren in de functie is het gebruik van de locatiegegevens afkomstig van de telemetrie van het apparaat in het voertuig voor het beoordelen van de geofence-status. In het geval dat een bepaald voertuig buiten de geofence gaat, verzamelt de functie meer informatie, zoals het adres van de locatie via de [Reverse API voor zoekadres .](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) Deze API vertaalt een bepaalde locatiecoördinaat naar een menselijk begrijpelijk adres. 

Alle relevante gebeurtenis-informatie wordt vervolgens bewaard in de blob-winkel. Stap 5 hieronder wijst op de uitvoerbare code die dergelijke logica implementeert. Volg de onderstaande stappen om een Azure-functie te maken die gegevenslogboeken naar de blobcontainer in het blob-opslagaccount verzendt en er een abonnement op het gebeurtenisraster aan toevoegt.

1. Selecteer in het Azure-portaldashboard de optie een resource maken. Selecteer **Berekenen** in de lijst met beschikbare resourcetypen en selecteer **Vervolgens Functie-app**.

    ![create-resource](./media/tutorial-iot-hub-maps/create-resource.png)

2. Geef op de pagina **Functie-app's** een naam aan uw functie-app. Selecteer **onder Resourcegroep**de optie **Bestaande**gebruiken en selecteer 'ContosoRental' in de vervolgkeuzelijst. Selecteer ".NET Core" als de Runtime Stack. Selecteer **onder Hosting**, voor **opslagaccount**de naam van het opslagaccount in een eerdere stap. In onze vorige stap hebben we de opslagaccount **v2storage**genoemd.  Selecteer vervolgens **Controleren+Maken**.
    
    ![maken-app](./media/tutorial-iot-hub-maps/rental-app.png)

2. Controleer de details van de functie-app en selecteer 'Maken'.

3. Zodra de app is gemaakt, moeten we er een functie aan toevoegen. Ga naar de functie-app. Klik **op Nieuwe functie** om een functie toe te voegen en kies **In-Portal** als ontwikkelomgeving. Selecteer vervolgens **Doorgaan**.

    ![maken-functie](./media/tutorial-iot-hub-maps/function.png)

4. Kies **Meer sjablonen** en klik op Sjablonen voltooien en **weergeven**. 

5. Selecteer de sjabloon met een **Azure Event Grid Trigger**. Installeer extensies als daarom wordt gevraagd, geef de functie een naam en selecteer **Maken**.

    ![functiesjabloon](./media/tutorial-iot-hub-maps/eventgrid-funct.png)
    
    De **Azure Event Hub Trigger** en de Azure Event Grid **Trigger** hebben vergelijkbare pictogrammen. Zorg ervoor dat u de **Azure Event Grid Trigger**selecteert.

6. Kopieer de [C#-code](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) naar uw functie.
 
7. Vervang in het Script C# de volgende parameters. Klik op **Opslaan**. Klik nog niet op **Uitvoeren**
    * Vervang de **SUBSCRIPTION_KEY** door de primaire abonnementssleutel van uw Azure Maps-account.
    * Vervang de **UDID** door de udId van de geofence die u hebt geüpload, 
    * Met de functie **CreateBlobAsync** in het script wordt een blob per gebeurtenis in het gegevensopslagaccount gemaakt. Vervang de **ACCESS_KEY,** **ACCOUNT_NAME**en **STORAGE_CONTAINER_NAME** door de toegangssleutel, accountnaam en gegevensopslagcontainer van uw opslagaccount.

10. Klik op **Abonnement gebeurtenisraster toevoegen**.
    
    ![add-event-grid](./media/tutorial-iot-hub-maps/add-egs.png)

11. Vul abonnementsgegevens in onder **EVENEMENT ABONNEMENT DETAILS** naam uw evenement abonnement. Kies voor gebeurtenisschema 'Gebeurtenisrasterschema'. Selecteer **onder ONDERWERP DETAILS** 'Azure IoT Hub-accounts' als onderwerptype. Kies hetzelfde abonnement dat u hebt gebruikt voor het maken van de resourcegroep en selecteer 'ContosoRental' als de 'Resourcegroep'. Kies de IoT-hub die u hebt gemaakt als 'Resource'. Kies **Apparaattelemetrie** als gebeurtenistype. Nadat u deze opties hebt gekozen, ziet u automatisch de wijziging van 'Onderwerptype' in 'IoT-hub'.

    ![event-grid-abonnement](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Gebeurtenissen filteren met de routebeschrijving van het IoT-hubbericht

Nadat u een abonnement op eventgrid hebt toegevoegd aan de Azure-functie, ziet u een standaardberichtroute naar gebeurtenisraster in het **berichtrouteblad van** IoT Hub. Met berichtroutering u verschillende gegevenstypen naar verschillende eindpunten routeren. U bijvoorbeeld telemetrieberichten van apparaten, levenscyclusgebeurtenissen van apparaten en gebeurtenissen voor apparaatwijzigingen routeren. Zie Het routeren van [IoT-hub-berichten gebruiken](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)voor meer informatie over het routeren van IoT-hub-berichten.

![hub-EG-route](./media/tutorial-iot-hub-maps/hub-route.png)

In ons voorbeeldscenario willen we alle berichten filteren waar het huurvoertuig in beweging is. Om dergelijke apparaattelemetriegebeurtenissen te publiceren naar gebeurtenisraster, gebruiken we de `Engine` routeringsquery om de gebeurtenissen te filteren waar de eigenschap **'AAN'** is. Er zijn verschillende manieren om IoT-device-to-cloud-berichten op te vragen, zie [IoT Hub-berichtroutering](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax)voor meer informatie over de syntaxis van berichtroutering. Als u een routeringsquery wilt maken, klikt u op de **route RouteToEventGrid** en vervangt u de **routeringsquery** door **'Engine='ON'** en klikt u op **Opslaan.** Nu publiceert iot-hub alleen apparaattelemetrie waar de engine is ingeschakeld.

![hub-EG-filter](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Telemetriegegevens verzenden naar IoT Hub

Zodra onze Azure-functie operationeel is, kunnen we nu telemetriegegevens verzenden naar de IoT-hub, die deze doorstuurt naar het gebeurtenisraster. Laten we een C#-toepassing gebruiken om locatiegegevens te simuleren voor een apparaat in het voertuig van een huurauto. Om de toepassing uit te voeren, hebt u de .NET Core SDK 2.1.0 of meer op uw ontwikkelingsmachine nodig. Volg de onderstaande stappen om gesimuleerde telemetriegegevens naar IoT Hub te verzenden.

1. Download het [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C# project. 

2. Open het simulatedCar.cs bestand in een teksteditor naar `connectionString` keuze en vervang de waarde van het bestand met de waarde die u hebt opgeslagen toen u het apparaat registreerde en sla wijzigingen in het bestand op.
 
3. Zorg ervoor dat je .NET Core op je machine hebt geïnstalleerd. Navigeer in het lokale terminalvenster naar de hoofdmap van het C#-project en voer de volgende opdracht uit om de vereiste pakketten voor gesimuleerde apparaattoepassing te installeren:
    
    ```cmd/sh
    dotnet restore
    ```

4. Voer in dezelfde terminal de volgende opdracht uit om de simulatietoepassing voor huurauto's te bouwen en uit te voeren:

    ```cmd/sh
    dotnet run
    ```

  Uw lokale terminal moet er uitzien als de onderstaande.

  ![Terminaloutput](./media/tutorial-iot-hub-maps/terminal.png)

Als u de blobopslagcontainer nu opent, moet u vier blobs kunnen zien voor locaties waar het voertuig zich buiten de geofence bevond.

![Blob invoeren](./media/tutorial-iot-hub-maps/blob.png)

De kaart hieronder toont vier punten waar het voertuig was buiten de geofence, geregistreerd op regelmatige tijdstippen.

![schendingkaart](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>Volgende stappen

Zie het alsvolgt in deze zelfstudie:

* [Zoekadres omgekeerd opvragen](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Download Geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Zie voor een volledige lijst met Azure Maps REST API's:

* [Azure Maps REST-API's](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Zie voor meer informatie over IoT Plug and Play:

* [IoT Plug en Play](https://docs.microsoft.com/azure/iot-pnp)

Ga naar:

* [Azure-gecertificeerde apparaten](https://catalog.azureiotsolutions.com/)

Zie voor meer informatie over het verzenden van apparaten naar cloudtelemetrie en andersom:

* [Telemetrie verzenden vanaf een apparaat](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

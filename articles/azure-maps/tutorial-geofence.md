---
title: 'Zelfstudie: Een geofence maken en apparaten op een kaart volgen | Microsoft Azure Maps'
description: Meer informatie over het instellen van een geofence en het bijhouden van apparaten ten opzichte van de geofence met behulp van Microsoft Azure Maps Spatial Service.
author: philmea
ms.author: philmea
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 126829f12d71e40511c26e781cb191988c1d031e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333871"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Zelfstudie: Een geofence instellen met Azure Maps

In deze zelfstudie doorloopt u de basisstappen voor het instellen van geofence met behulp van Azure Maps. Overweeg dit scenario, een bouwplaats manager heeft om potentiële gevaarlijke apparatuur te controleren. De manager moet ervoor zorgen dat de apparatuur blijft in de gekozen algemene bouwgebieden. Dit totale bouwgebied is een harde parameter. Regelgeving vereist apparatuur om binnen deze parameter te blijven en schendingen worden gemeld aan de Operations Manager.  

We gebruiken de Data Upload API om een geofence op te slaan en de Geofence API te gebruiken om de locatie van de apparatuur ten opzichte van de geofence te controleren. Zowel de Data Upload API als de Geofence API zijn afkomstig van Azure Maps. We gebruiken azure event grid ook om de geofence-resultaten te streamen en een melding in te stellen op basis van de geofence-resultaten. Zie [Wat is Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) voor meer informatie over Event Grid.

In deze tutorial behandelen we hoe:

> [!div class="checklist"]
> * Een geofence-gebied uploaden in de gegevensservices van Azure Maps met behulp van de Data Upload-API.
> *   Een Event Grid instellen voor het afhandelen van geofence-gebeurtenissen.
> *   Stel een Geofence-gebeurtenis-handler in.
> *   Logic Apps gebruiken om waarschuwingen in te stellen als reactie op geofence-gebeurtenissen.
> *   De geofence-service-API's van Azure Maps gebruiken om te volgen of een bouwmachine zich op het bouwterrein bevindt.


## <a name="prerequisites"></a>Vereisten

### <a name="create-an-azure-maps-account"></a>Een Azure Maps-account maken 

Volg instructies in [Een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) om een Azure Maps-accountabonnement te maken met s1-prijscategorie. De stappen in [de primaire sleutel krijgen,](quick-demo-map-app.md#get-the-primary-key-for-your-account) laten zien hoe u de primaire sleutel van uw account ophalen. Zie Verificatie beheren in Azure [Maps voor](./how-to-manage-authentication.md)meer informatie over verificatie in Azure Maps.

## <a name="upload-geofences"></a>Geofences uploaden

We gaan ervan uit dat de belangrijkste geofence subsite1 is, die een ingestelde vervaldatum heeft. U kunt meer geneste geofences maken overeenkomstig uw vereisten. Deze sets van hekken kunnen worden gebruikt om verschillende bouwgebieden binnen het totale bouwgebied te volgen. Subsite1 kan bijvoorbeeld zijn waar in week 1 tot en met 4 van het schema wordt gewerkt. subsite2 kan zijn waar het werk plaatsvindt in week 5 tot en met 7. Al deze hekken kunnen aan het begin van het project als één gegevensset worden geladen. Deze hekken worden gebruikt om regels te volgen op basis van tijd en ruimte. 

Om de geofence voor de bouwplaats te uploaden met behulp van de Data Upload API, gebruiken we de postbodeapplicatie. Installeer de [postbode applicatie](https://www.getpostman.com/) en maak een gratis account. 

Zodra de Postman-app is geïnstalleerd, voert u deze stappen uit om de geofence van de bouwplaats te uploaden met behulp van de API voor gegevensuploaden van Azure Maps.

1. Open de Postman-app en klik op New | Create new en selecteer Request. Voer een Request-naam in voor Upload geofence data. Selecteer een verzameling of map waarin u deze wilt opslaan en klik op Save.

    ![Geofences uploaden met Postman](./media/tutorial-geofence/postman-new.png)

2. Selecteer de methode HTTP POST op het tabblad Builder en voer de volgende URL in om een POST-aanvraag te doen.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    De GEOJSON-parameter in het URL-pad geeft de gegevensindeling aan van de gegevens die worden geüpload.

3. Klik op **Params** en voer de volgende sleutel/waarde-paren in die voor de POST-aanvraag-URL moeten worden gebruikt. Vervang {subscription-key} door uw Azure Maps-abonnementssleutel, ook wel primaire sleutel genoemd.
   
    ![Parameters voor het uploaden van gegevens (geofence) in Postman](./media/tutorial-geofence/postman-key-vals.png)

4. Klik **op Hoofdtekst** en selecteer vervolgens de ruwe invoerindeling en kies JSON als invoerindeling in de vervolgkeuzelijst. Geef de volgende JSON op als de gegevens die u wilt uploaden:

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
              "expiredTime": "2019-01-15T00:00:00",
              "validityPeriod": [
                {
                  "startTime": "2019-01-08T01:00:00",
                  "endTime": "2019-01-08T17:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. Klik op Send en controleer de reactieheader. Op een geslaagd verzoek bevat de **koplocatie** de status URI. De status URI is van de volgende indeling. De waarde uploadStatusId ligt niet tussen { }. Het is gebruikelijk om { } te gebruiken om waarden weer te geven die de gebruiker moet invoeren of waarden die verschillend zijn voor verschillende gebruikers.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Kopieer uw status URI en append de abonnementssleutel. De status URI-indeling moet worden als de onderstaande. Merk op dat u in de onderstaande indeling de {subscription-key} zou wijzigen, de { }, niet met uw abonnementssleutel.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Als u `udId`het tabblad > Openen opent in de Postman-app en selecteert u DE METHODE GET HTTP op het tabblad Bouwer. Een GET-aanvraag indienen bij de status URI van de vorige stap. Als het uploaden van uw gegevens succesvol is, ontvangt u de udId in de reactieinstantie. Kopieer de udId voor later gebruik.

   ```JSON
   {
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udId}?api-version=1.0"
   }
   ```

## <a name="set-up-an-event-handler"></a>Een gebeurtenis-handler instellen

In deze sectie maken we een gebeurtenishandler die meldingen ontvangt. Deze gebeurtenishandler moet de Operations Manager op de hoogte stellen van enter- en exitgebeurtenissen van apparatuur.

We maken twee [Logic Apps-services](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) voor het afhandelen van enter- en exit-evenementen. Wanneer de gebeurtenissen in de Logische apps worden geactiveerd, worden meer gebeurtenissen achter elkaar geactiveerd. Het idee is om waarschuwingen te sturen, in dit geval e-mails, naar de Operations Manager. De volgende afbeelding illustreert het maken van een Logic App voor Enter-gebeurtenis voor de geofence. Op eenzelfde manier kunt u een Logic App maken voor een Exit-gebeurtenis voor de geofence. U kun alle [ondersteunde gebeurtenis-handlers](https://docs.microsoft.com/azure/event-grid/event-handlers) bekijken voor meer informatie.

1. Maak een Logische App in Azure-portal. Selecteer de Logische app in Azure Marketplace. Selecteer vervolgens de knop **Maken.**

   ![Azure Logic Apps maken om geofence-gebeurtenissen te verwerken](./media/tutorial-geofence/logic-app.png)

2. Navigeer in het instellingenmenu voor de Logic App naar **Logic App Designer**

3. Selecteer een HTTP-aanvraagtrigger en selecteer vervolgens 'Nieuwe stap'. Selecteer in de outlook-connector de optie 'een e-mail verzenden' als actie
  
   ![Logic Apps-schema](./media/tutorial-geofence/logic-app-schema.png)

4. Vul de velden in voor het verzenden van een e-mail. Laat de HTTP-URL, zal het automatisch genereren nadat u op "opslaan" klikt

   ![Een eindpunt van Logic Apps genereren](./media/tutorial-geofence/logic-app-endpoint.png)

5. Sla de logische app op om het HTTP-URL-eindpunt te genereren en de HTTP-URL te kopiëren.

## <a name="create-an-azure-maps-events-subscription"></a>Een abonnement maken op Azure Maps-evenementen

Azure Maps ondersteunt drie typen gebeurtenissen. [Hier](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps) vindt u een overzicht van de gebeurtenistypen die door Azure Maps worden ondersteund. We hebben twee verschillende evenementabonnementen nodig, één voor het enter-evenement en een voor de exit-evenementen.

Volg de stappen hieronder om een gebeurtenisabonnement te maken voor Enter-gebeurtenissen voor de geofence. U kunt zich op een vergelijkbare manier abonneren op Exit-gebeurtenissen voor de geofence.

1. Navigeer naar uw Azure Maps-account. Selecteer Abonnementen in het dashboard. Klik op de naam van uw abonnement en selecteer **gebeurtenissen** in het instellingenmenu.

   ![Navigeren naar Azure Maps-accountgebeurtenissen](./media/tutorial-geofence/events-tab.png)

2. U maakt een gebeurtenisabonnement door Gebeurtenisabonnement te selecteren op de pagina Gebeurtenissen.

   ![Een abonnement maken op Azure Maps-evenementen](./media/tutorial-geofence/create-event-subscription.png)

3. Geef het gebeurtenisabonnement een naam en abonneer u op het gebeurtenistype Enter. Selecteer nu Webhaak als 'Eindpunttype'. Klik op 'Selecteer een eindpunt' en kopieer het HTTP URL-eindpunt van uw Logic App naar {Endpoint}.

   ![Details van azure Maps Events-abonnementen](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Geofence-API gebruiken

U de Geofence API gebruiken om te controleren of een **apparaat**, in dit geval apparatuur, zich binnen of buiten een geofence bevindt. Hiermee u de Geofence GET API opvragen tegen verschillende locaties, waar een bepaalde apparatuur in de loop van de tijd is verplaatst. Het volgende cijfer illustreert vijf locaties met vijf bouwmachines. 

> [!Note]
> Het scenario en gedrag is gebaseerd op dezelfde **apparaat-id,** zodat deze de vijf verschillende locaties weergeeft zoals in de onderstaande figuur.

De "deviceId" is een unieke ID die u voor uw apparaat verstrekt in het GET-verzoek, wanneer u naar de locatie vraagt. Wanneer u een asynchrone aanvraag indient voor de **zoekgeofence - GET API,** helpt de "deviceId" bij het publiceren van geofence-gebeurtenissen voor dat apparaat ten opzichte van de opgegeven geofence. In deze zelfstudie hebben we asynchrone verzoeken ingediend bij de API met een unieke "deviceId". De aanvragen in de zelfstudie worden in chronologische volgorde uitgevoerd, zoals in het diagram. De eigenschap "isEventPublished" in het antwoord wordt gepubliceerd wanneer een apparaat de geofence binnenkomt of verlaat. U hoeft geen apparaat te registreren om te volgen met deze zelfstudie.

Laten we terugkijken op het diagram. Elk van deze vijf locaties wordt gebruikt om de gewijzigde Enter- en Exit-status van de geofence te beoordelen ten aanzien van de omheining. Wanneer er een statuswijziging optreedt, activeert de geofence-trigger een gebeurtenis, die door de Event Grid naar de Logic App wordt verzonden. Als gevolg hiervan ontvangt de beheerder van de bewerking de bijbehorende in- of exit-melding via een e-mail.

![Geofence-kaart in Azure Maps](./media/tutorial-geofence/geofence.png)

Open in de Postman-app een nieuw tabblad in de verzameling die u hierboven hebt gemaakt. Selecteer de methode GET HTTP op het tabblad Builder:

Hieronder volgen vijf HTTP GET Geofencing API-aanvragen, met verschillende locatiecoördinaten van de apparatuur. De coördinaten zijn zoals waargenomen in chronologische volgorde. Elke aanvraag wordt gevolgd door de hoofdtekst van de reactie.
 
1. Locatie 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Geofence-query 1](./media/tutorial-geofence/geofence-query1.png)

   In de reactie hierboven, de negatieve afstand van de belangrijkste geofence betekent dat de apparatuur is in de geofence. De positieve afstand van de subsite geofence betekent dat de apparatuur zich buiten de subsite geofence bevindt. 

2. Locatie 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Geofence-query 2](./media/tutorial-geofence/geofence-query2.png)

   Als je kijkt naar de voorgaande JSON reactie zorgvuldig de apparatuur is buiten de subsite, maar het is binnen het hoofdhek. Er wordt geen gebeurtenis geactiveerd en er wordt geen e-mail verzonden.

3. Locatie 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geofence-query 3](./media/tutorial-geofence/geofence-query3.png)

   Er is een statuswijziging opgetreden en de apparatuur bevindt zich nu binnen de geofence van de hoofdlocatie en de sublocatie. Deze wijziging zorgt ervoor dat een gebeurtenis wordt gepubliceerd en een meldingsmail wordt verzonden naar operations manager.

4. Locatie 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Geofence-query 4](./media/tutorial-geofence/geofence-query4.png)

   Als u de desbetreffende reactie zorgvuldig observeert, ziet u dat er hier geen gebeurtenis wordt gepubliceerd terwijl de apparatuur de geofence van de sublocatie wel heeft verlaten. Als u de opgegeven tijd van de gebruiker in de GET-aanvraag bekijkt, u zien dat de geofence van de subsite voor deze tijd is verlopen. De apparatuur is nog steeds in de belangrijkste geofence. U ziet ook dat de geometrie-id van de geofence van de sublocatie zich bevindt onder `expiredGeofenceGeometryId` in de hoofdtekst van de reactie.


5. Locatie 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geofence-query 5](./media/tutorial-geofence/geofence-query5.png)

   U kunt zien dat de apparatuur de geofence van de hoofdbouwplaats heeft verlaten. Er wordt een gebeurtenis gepubliceerd en er wordt een waarschuwingse-mail verzonden naar de Operations Manager.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heb je geleerd: hoe je geofence instelt door deze te uploaden in de Azure Maps and Data-service met behulp van de Api voor het uploaden van gegevens. U hebt ook geleerd hoe u Events Grid van Azure Maps gebruikt om u te abonneren op geofence-gebeurtenissen en deze af te handelen. 

* Zie [inhoudstypen in Azure Logic Apps verwerken](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type) voor informatie over hoe u Logic Apps gebruikt om JSON te parseren voor het bouwen van een complexere logica.
* Zie [Event handlers in Azure Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers) (Gebeurtenis-handlers in Azure Event Grid) voor meer informatie over gebeurtenis-handlers in Event Grid.

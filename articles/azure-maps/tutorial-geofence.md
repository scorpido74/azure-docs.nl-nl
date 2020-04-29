---
title: 'Zelf studie: een geofence maken en apparaten bijhouden op een kaart | Microsoft Azure kaarten'
description: Meer informatie over het instellen van een geofence en het bijhouden van apparaten ten opzichte van de geofence met behulp van Microsoft Azure Maps Spatial service.
author: philmea
ms.author: philmea
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 126829f12d71e40511c26e781cb191988c1d031e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80333871"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Zelf studie: een geofence instellen met behulp van Azure Maps

In deze zelfstudie doorloopt u de basisstappen voor het instellen van geofence met behulp van Azure Maps. Bekijk dit scenario, een bouw Site Manager moet de mogelijke gevaarlijke apparatuur bewaken. De manager moet er zeker van zijn dat de apparatuur in de gekozen algemene constructie gebieden blijft. Deze algemene constructie ruimte is een harde para meter. Voor Schriften is vereist dat uitrusting binnen deze para meter blijft en er worden schendingen gerapporteerd aan de Operations Manager.  

We gebruiken de Data upload API om een geofence op te slaan en de geofence API te gebruiken om de locatie van de apparatuur ten opzichte van de geofence te controleren. Zowel de Data upload-API als de geofence-API zijn van Azure Maps. We gebruiken ook Azure Event Grid om de geofence-resultaten te streamen en een melding in te stellen op basis van de resultaten van de geofence. Zie [Wat is Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) voor meer informatie over Event Grid.

In deze zelf studie hebben we betrekking op het volgende:

> [!div class="checklist"]
> * Een geofence-gebied uploaden in de gegevensservices van Azure Maps met behulp van de Data Upload-API.
> *   Een Event Grid instellen voor het afhandelen van geofence-gebeurtenissen.
> *   Stel een Geofence-gebeurtenis-handler in.
> *   Logic Apps gebruiken om waarschuwingen in te stellen als reactie op geofence-gebeurtenissen.
> *   De geofence-service-API's van Azure Maps gebruiken om te volgen of een bouwmachine zich op het bouwterrein bevindt.


## <a name="prerequisites"></a>Vereisten

### <a name="create-an-azure-maps-account"></a>Een Azure Maps-account maken 

Volg de instructies in [een account maken](quick-demo-map-app.md#create-an-account-with-azure-maps) om een Azure Maps account-abonnement te maken met de prijs categorie S1. Met de stappen in de [primaire sleutel ophalen](quick-demo-map-app.md#get-the-primary-key-for-your-account) wordt uitgelegd hoe u de primaire sleutel van uw account kunt ophalen. Zie [verificatie beheren in azure Maps](./how-to-manage-authentication.md)voor meer informatie over verificatie in azure Maps.

## <a name="upload-geofences"></a>Geofences uploaden

We gaan ervan uit dat de hoofd geofence subsite1 is, die een ingestelde verloop tijd heeft. U kunt meer geneste geofences maken overeenkomstig uw vereisten. Deze sets met omheiningen kunnen worden gebruikt voor het bijhouden van verschillende constructie gebieden binnen de algehele bouw terrein. Zo kan subsite1 bijvoorbeeld zijn waar werk plaatsvindt tijdens de week 1 tot 4 van het schema. subsite2 kan worden gebruikt in plaats van de week 5 tot en met 7. Alle dergelijke omheiningen kunnen aan het begin van het project als één gegevensset worden geladen. Deze omheiningen worden gebruikt voor het bijhouden van regels op basis van tijd en ruimte. 

Voor het uploaden van de geofence voor de bouw site met behulp van de Data upload-API, gebruiken we de toepassing postman. Installeer de [toepassing postman](https://www.getpostman.com/) en maak een gratis account. 

Nadat de Postman-app is geïnstalleerd, volgt u deze stappen om de geofence van de bouw site te uploaden met behulp van de Azure Maps Data upload-API.

1. Open de Postman-app en klik op New | Create new en selecteer Request. Voer een Request-naam in voor Upload geofence data. Selecteer een verzameling of map waarin u deze wilt opslaan en klik op Save.

    ![Geofences uploaden met Postman](./media/tutorial-geofence/postman-new.png)

2. Selecteer de methode HTTP POST op het tabblad Builder en voer de volgende URL in om een POST-aanvraag te doen.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    De GEOJSON-parameter in het URL-pad geeft de gegevensindeling aan van de gegevens die worden geüpload.

3. Klik op **Params** en voer de volgende sleutel/waarde-paren in die voor de POST-aanvraag-URL moeten worden gebruikt. Vervang {Subscription-Key} door uw Azure Maps-abonnements sleutel, ook wel primaire sleutel genoemd.
   
    ![Para meters voor het uploaden van gegevens (geofence) in postman](./media/tutorial-geofence/postman-key-vals.png)

4. Klik op **hoofd tekst** en selecteer vervolgens de indeling Onbewerkte invoer en kies JSON als invoer indeling in de vervolg keuzelijst. Geef de volgende JSON op als de gegevens die u wilt uploaden:

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

5. Klik op Send en controleer de reactieheader. Bij een geslaagde aanvraag bevat de **locatie** header de status-URI. De status-URI heeft de volgende indeling. De waarde van uploadStatusId is niet tussen {}. Het is een veelvoorkomende procedure om {} te gebruiken om waarden weer te geven die de gebruiker moet invoeren, of waarden die verschillend zijn voor een andere gebruiker.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Kopieer uw status-URI en voeg de abonnements sleutel toe. De URI-indeling van de status moet er als volgt uitzien. In de onderstaande notatie zou u de {Subscription-Key} wijzigen, met inbegrip van {}, met uw abonnements sleutel.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Als u de `udId`wilt weer geven, opent u een nieuw tabblad in de app postman en selecteert u HTTP-methode ophalen op het tabblad opbouw functie. Maak een GET-aanvraag op de status-URI van de vorige stap. Als het uploaden van uw gegevens is geslaagd, ontvangt u de udId in de hoofd tekst van het antwoord. Kopieer de udId voor later gebruik.

   ```JSON
   {
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udId}?api-version=1.0"
   }
   ```

## <a name="set-up-an-event-handler"></a>Een gebeurtenis-handler instellen

In deze sectie maken we een gebeurtenis-handler die meldingen ontvangt. Deze gebeurtenis-handler moet de Operations Manager informeren over de invoer-en afsluit gebeurtenissen van alle apparatuur.

We maken twee [Logic apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) Services voor het afhandelen van ENTER-en Exit-gebeurtenissen. Wanneer de gebeurtenissen in de Logic Apps trigger, worden er meer gebeurtenissen geactiveerd in de reeks. Het is verstandig om in dit geval e-mail berichten te verzenden naar de Operations Manager. De volgende afbeelding illustreert het maken van een Logic App voor Enter-gebeurtenis voor de geofence. Op eenzelfde manier kunt u een Logic App maken voor een Exit-gebeurtenis voor de geofence. U kun alle [ondersteunde gebeurtenis-handlers](https://docs.microsoft.com/azure/event-grid/event-handlers) bekijken voor meer informatie.

1. Een logische app maken in Azure Portal. Selecteer de logische app in azure Marketplace. Selecteer vervolgens de knop **maken** .

   ![Azure Logic Apps maken voor het verwerken van geofence-gebeurtenissen](./media/tutorial-geofence/logic-app.png)

2. Ga in het menu instellingen voor de logische app naar **Logic app Designer**

3. Selecteer een HTTP-aanvraag trigger en selecteer vervolgens ' nieuwe stap '. Selecteer in de Outlook-Connector "een e-mail verzenden" als actie
  
   ![Logic Apps-schema](./media/tutorial-geofence/logic-app-schema.png)

4. Vul de velden in voor het verzenden van een e-mail. De HTTP-URL verlaten, wordt deze automatisch gegenereerd nadat u op Opslaan hebt geklikt

   ![Een Logic Apps-eind punt genereren](./media/tutorial-geofence/logic-app-endpoint.png)

5. Sla de logische app op om het HTTP-URL-eindpunt te genereren en de HTTP-URL te kopiëren.

## <a name="create-an-azure-maps-events-subscription"></a>Een abonnement maken op Azure Maps-evenementen

Azure Maps ondersteunt drie typen gebeurtenissen. [Hier](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps) vindt u een overzicht van de gebeurtenistypen die door Azure Maps worden ondersteund. Er zijn twee verschillende gebeurtenis abonnementen nodig, één voor de gebeurtenis Enter en één voor de afsluit gebeurtenissen.

Volg de stappen hieronder om een gebeurtenisabonnement te maken voor Enter-gebeurtenissen voor de geofence. U kunt zich op een vergelijkbare manier abonneren op Exit-gebeurtenissen voor de geofence.

1. Navigeer naar uw Azure Maps-account. Selecteer in het dash board abonnementen. Klik op de naam van uw abonnement en selecteer **gebeurtenissen** in het menu instellingen.

   ![Navigeren naar Azure Maps-account gebeurtenissen](./media/tutorial-geofence/events-tab.png)

2. U maakt een gebeurtenisabonnement door Gebeurtenisabonnement te selecteren op de pagina Gebeurtenissen.

   ![Een abonnement maken op Azure Maps-evenementen](./media/tutorial-geofence/create-event-subscription.png)

3. Geef het gebeurtenisabonnement een naam en abonneer u op het gebeurtenistype Enter. Selecteer nu webhook als "eindpunt type". Klik op Selecteer een eind punt en kopieer het eind punt van de logische app-URL naar {Endpoint}

   ![Details van abonnement voor Azure Maps gebeurtenissen](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Geofence-API gebruiken

U kunt de geofence API gebruiken om te controleren of een **apparaat**, in dit geval apparatuur, zich binnen of buiten een geofence bevindt. Hiermee kunt u een query uitvoeren op de geofence-API op verschillende locaties, waarbij een bepaalde apparatuur na verloop van tijd is verplaatst. In de volgende afbeelding ziet u vijf locaties met vijf constructie apparatuur. 

> [!Note]
> Het scenario en gedrag is gebaseerd op dezelfde **apparaat-id** , zodat de vijf verschillende locaties overeenkomen met de onderstaande afbeelding.

De ' deviceId ' is een unieke ID die u in de GET-aanvraag voor uw apparaat opgeeft, bij het uitvoeren van query's op de locatie. Wanneer u een asynchrone aanvraag voor de **geofence van de zoek opdracht**maakt, helpt de deviceId bij het publiceren van geofence-gebeurtenissen voor dat apparaat ten opzichte van de opgegeven geofence. In deze zelf studie hebben we asynchrone aanvragen voor de API met een unieke ' deviceId ' gemaakt. De aanvragen in de zelf studie worden in chronologische volg orde gemaakt, zoals in het diagram. De eigenschap isEventPublished in het antwoord wordt gepubliceerd wanneer een apparaat de geofence binnengaat of verlaat. U hoeft een apparaat niet te registreren om te volgen met deze zelf studie.

Hiermee wordt weer gegeven in het diagram. Elk van deze vijf locaties wordt gebruikt om de gewijzigde Enter- en Exit-status van de geofence te beoordelen ten aanzien van de omheining. Wanneer er een statuswijziging optreedt, activeert de geofence-trigger een gebeurtenis, die door de Event Grid naar de Logic App wordt verzonden. Als gevolg hiervan ontvangt de Manager van de bewerking de bijbehorende melding voor het invoeren of afsluiten via een e-mail bericht.

![Geofence-toewijzing in Azure Maps](./media/tutorial-geofence/geofence.png)

Open in de Postman-app een nieuw tabblad in de verzameling die u hierboven hebt gemaakt. Selecteer de methode GET HTTP op het tabblad Builder:

Hier volgen vijf HTTP-aanvragen voor geoomheiningen van de API met verschillende locatie coördinaten van de apparatuur. De coördinaten worden in chronologische volg orde gemeten. Elke aanvraag wordt gevolgd door de hoofdtekst van de reactie.
 
1. Locatie 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Geofence-query 1](./media/tutorial-geofence/geofence-query1.png)

   In het bovenstaande antwoord betekent de negatieve afstand van de hoofd geofence dat het apparaat zich in de geofence bevindt. De positieve afstand van de geofence van de subsite betekent dat de apparatuur zich buiten de geofence van de subsite bevindt. 

2. Locatie 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Geofence-query 2](./media/tutorial-geofence/geofence-query2.png)

   Als u de voor gaande JSON-antwoorden zorgvuldig bekijkt, is het apparaat buiten de subsite, maar het bevindt zich in de hoofd omheining. Er wordt geen gebeurtenis geactiveerd en er wordt geen e-mail bericht verzonden.

3. Locatie 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geofence-query 3](./media/tutorial-geofence/geofence-query3.png)

   Er is een statuswijziging opgetreden en de apparatuur bevindt zich nu binnen de geofence van de hoofdlocatie en de sublocatie. Met deze wijziging wordt een gebeurtenis gepubliceerd en wordt er een e-mail melding verzonden naar de Operations Manager.

4. Locatie 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Geofence-query 4](./media/tutorial-geofence/geofence-query4.png)

   Als u de desbetreffende reactie zorgvuldig observeert, ziet u dat er hier geen gebeurtenis wordt gepubliceerd terwijl de apparatuur de geofence van de sublocatie wel heeft verlaten. Als u de opgegeven tijd van de gebruiker in de GET-aanvraag bekijkt, kunt u zien dat de geofence van de subsite voor deze tijd is verlopen. De apparatuur bevindt zich nog steeds in de hoofd geofence. U ziet ook dat de geometrie-id van de geofence van de sublocatie zich bevindt onder `expiredGeofenceGeometryId` in de hoofdtekst van de reactie.


5. Locatie 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geofence-query 5](./media/tutorial-geofence/geofence-query5.png)

   U kunt zien dat de apparatuur de geofence van de hoofdbouwplaats heeft verlaten. Er wordt een gebeurtenis gepubliceerd en er wordt een e-mail bericht verzonden naar de Operations Manager.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u het volgende geleerd: geofence instellen door deze te uploaden in de Azure Maps en gegevens service met de API voor het uploaden van gegevens. U hebt ook geleerd hoe u Events Grid van Azure Maps gebruikt om u te abonneren op geofence-gebeurtenissen en deze af te handelen. 

* Zie [inhoudstypen in Azure Logic Apps verwerken](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type) voor informatie over hoe u Logic Apps gebruikt om JSON te parseren voor het bouwen van een complexere logica.
* Zie [Event handlers in Azure Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers) (Gebeurtenis-handlers in Azure Event Grid) voor meer informatie over gebeurtenis-handlers in Event Grid.

---
title: 'Zelf studie: een geofence maken met behulp van Azure Maps'
description: 'Zelf studie: een geofence instellen met behulp van Azure Maps.'
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 62005546c653796773083eaf625820ab532a8a2c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74107095"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Zelf studie: een geofence instellen met behulp van Azure Maps

In deze zelfstudie doorloopt u de basisstappen voor het instellen van geofence met behulp van Azure Maps. Het scenario in deze zelfstudie helpt bouwopzichters om mogelijke gevaarlijke machines te bewaken die zich buiten het aangewezen bouwterrein begeven. Op een bouwterrein staan dure machines en gelden strenge voorschriften. Doorgaans moet de apparatuur op het bouwterrein blijven en mag deze het terrein niet zonder toestemming verlaten.

We gebruiken Azure Maps Upload-API om een geofence op te slaan, en we gebruiken Azure Maps Geofence-API om de locatie van de apparatuur ten opzichte van de geofence te controleren. We gebruiken Azure Event Grid om de geofence-resultaten te streamen en op basis van deze resultaten een melding op te stellen.
Zie [Wat is Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) voor meer informatie over Event Grid.
In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een geofence-gebied uploaden in de gegevensservices van Azure Maps met behulp van de Data Upload-API.
> *   Een Event Grid instellen voor het afhandelen van geofence-gebeurtenissen.
> *   Stel een Geofence-gebeurtenis-handler in.
> *   Logic Apps gebruiken om waarschuwingen in te stellen als reactie op geofence-gebeurtenissen.
> *   De geofence-service-API's van Azure Maps gebruiken om te volgen of een bouwmachine zich op het bouwterrein bevindt.


## <a name="prerequisites"></a>Vereisten

### <a name="create-an-azure-maps-account"></a>Een Azure Maps-account maken 

Als u de stappen in deze zelf studie wilt uitvoeren, volgt u de instructies in [Manage account](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) om een abonnement voor een Azure Maps account te maken met de prijs categorie S1 en volgt u de stappen in [primaire sleutel ophalen](./tutorial-search-location.md#getkey) om de primaire abonnements sleutel voor uw account op te halen.

## <a name="upload-geofences"></a>Geofences uploaden

We hebben de toepassing Postman gebruikt om de geofence voor het bouwterrein te uploaden met de Data Upload-API. In deze zelfstudie gaan we ervan uit dat er één gebied is voor het hele bouwterrein, in dit geval een vaste parameter die niet mag worden geschonden door de bouwmachines. Het passeren van deze omheining is een ernstige overtreding die moet worden gemeld bij de Operations Manager. Er kan een geoptimaliseerde reeks aanvullende omheiningen worden gebruikt als u volgens een schema verschillende bouwgebieden binnen het totale bouwterrein wilt volgen. We kunnen ervan uitgaan dat de hoofd-geofence een subsite1 met een ingestelde vervaltijd heeft, en na dat tijdstip vervalt. U kunt meer geneste geofences maken overeenkomstig uw vereisten. Zo zou subsite1 bijvoorbeeld de plek kunnen zijn waar van week 1 tot en met 4 van de planning wordt gewerkt, en subsite2 de plek waar van week 5 tot en met 7 wordt gewerkt. Alle dergelijke omheiningen kunnen aan het begin van het project als één gegevensset worden geladen en worden gebruikt voor het bijhouden van regels op basis van tijd en ruimte. Zie [Geofence GeoJSON data](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) (Geofence-GeoJSON-gegevens) voor meer informatie over de indeling van geofence-gegevens. Zie de [documentatie bij de Data Upload-API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) voor meer informatie over het uploaden van gegevens naar de service Azure Maps.

Open de Postman-app en volg de volgende stappen om de geofence van de bouwplaats te uploaden met behulp van de Data Upload-API van Azure Maps.

1. Open de Postman-app en klik op New | Create new en selecteer Request. Voer een Request-naam in voor Upload geofence data. Selecteer een verzameling of map waarin u deze wilt opslaan en klik op Save.

    ![Geofences uploaden met Postman](./media/tutorial-geofence/postman-new.png)

2. Selecteer de methode HTTP POST op het tabblad Builder en voer de volgende URL in om een POST-aanvraag te doen.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    De GEOJSON-parameter in het URL-pad geeft de gegevensindeling aan van de gegevens die worden geüpload.

3. Klik op **Params** en voer de volgende sleutel/waarde-paren in die voor de POST-aanvraag-URL moeten worden gebruikt. Vervang de abonnements sleutel waarde door uw Azure Maps primaire abonnements sleutel.
   
    ![Key-Value-parameters in Postman](./media/tutorial-geofence/postman-key-vals.png)

4. Klik op **Body** en selecteer de onbewerkte invoerindeling. Kies de invoerindeling JSON in de vervolgkeuzelijst. Geef de volgende JSON op als de gegevens die u wilt uploaden:

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

5. Klik op Send en controleer de reactieheader. Bij een geslaagde aanvraag bevat de **locatie** header de status-URI om de huidige status van de upload aanvraag te controleren. De status-URI heeft de volgende indeling. 

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

## <a name="set-up-an-event-handler"></a>Een gebeurtenis-handler instellen

Om de Operations Manager op de hoogte te brengen van Enter- en Exit-gebeurtenissen, moet er een gebeurtenis-handler worden gemaakt die de meldingen ontvangt.

We gaan twee [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps)-services maken voor het afhandelen van Enter- en Exit-gebeurtenissen. We gaan in de Logic Apps ook gebeurtenistriggers maken die door deze gebeurtenissen worden geactiveerd. Het is de bedoeling dat er waarschuwingen worden verzonden - in dit geval e-mails naar de Operations Manager - wanneer machines de bouwplaats binnenkomen (Enter) of verlaten (Exit). De volgende afbeelding illustreert het maken van een Logic App voor Enter-gebeurtenis voor de geofence. Op eenzelfde manier kunt u een Logic App maken voor een Exit-gebeurtenis voor de geofence.
U kun alle [ondersteunde gebeurtenis-handlers](https://docs.microsoft.com/azure/event-grid/event-handlers) bekijken voor meer informatie.

1. Een Logic App maken in Azure-portal

   ![Logic Apps maken](./media/tutorial-geofence/logic-app.png)

2. Selecteer een HTTP-aanvraagtrigger en selecteer vervolgens de actie ‘E-mail verzenden’ in de outlook-connector
  
   ![Logic Apps-schema](./media/tutorial-geofence/logic-app-schema.png)

3. Sla de logische app op om het HTTP-URL-eindpunt te genereren en de HTTP-URL te kopiëren.

   ![Logic Apps-eindpunt](./media/tutorial-geofence/logic-app-endpoint.png)


## <a name="create-an-azure-maps-events-subscription"></a>Een abonnement maken op Azure Maps-evenementen

Azure Maps ondersteunt drie typen gebeurtenissen. [Hier](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps) vindt u een overzicht van de gebeurtenistypen die door Azure Maps worden ondersteund. We gaan twee verschillende abonnementen maken, één voor Enter-gebeurtenissen en één voor Exit-gebeurtenissen.

Volg de stappen hieronder om een gebeurtenisabonnement te maken voor Enter-gebeurtenissen voor de geofence. U kunt zich op een vergelijkbare manier abonneren op Exit-gebeurtenissen voor de geofence.

1. Navigeer naar uw Azure Maps-account via [deze portal-koppeling](https://ms.portal.azure.com/#@microsoft.onmicrosoft.com/dashboard/) en selecteer het tabblad Gebeurtenissen.

   ![Azure Maps-gebeurtenissen](./media/tutorial-geofence/events-tab.png)

2. U maakt een gebeurtenisabonnement door Gebeurtenisabonnement te selecteren op de pagina Gebeurtenissen.

   ![Abonnement op Azure Maps-gebeurtenissen](./media/tutorial-geofence/create-event-subscription.png)

3. Geef het gebeurtenisabonnement een naam en abonneer u op het gebeurtenistype Enter. Selecteer nu Webhook als Eindpunttype en kopieer het HTTP-URL-eindpunt van de logische app in 'Eindpunt'

   ![Gebeurtenisabonnement](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Geofence-API gebruiken

U kunt de Geofence-API gebruiken om te controleren of een **apparaat** (apparatuur maakt deel uit van de status) zich binnen of buiten een geofence bevindt. Meer inzicht in de GET API van de geofence. We vragen deze op voor verschillende locaties waarnaar een bepaalde apparatuur na verloop van tijd is verplaatst. De volgende afbeelding illustreert vijf locaties van een bepaalde bouwmachine met een unieke **apparaat-id**, zoals deze in chronologische volgorde zijn waargenomen. Elk van deze vijf locaties wordt gebruikt om de gewijzigde Enter- en Exit-status van de geofence te beoordelen ten aanzien van de omheining. Wanneer er een statuswijziging optreedt, activeert de geofence-trigger een gebeurtenis, die door de Event Grid naar de Logic App wordt verzonden. Als gevolg hiervan ontvangt de Operations Manager via e-mail de bijbehorende Enter- of Exit-melding.

> [!Note]
> Bovenstaand scenario en gedrag zijn gebaseerd op dezelfde **apparaat-id**. Deze reflecteren dus de vijf verschillende locaties zoals in de afbeelding hieronder.

![Geofence-kaart](./media/tutorial-geofence/geofence.png)

Open in de Postman-app een nieuw tabblad in de verzameling die u hierboven hebt gemaakt. Selecteer de methode GET HTTP op het tabblad Builder:

Hieronder vindt u vijf GET HTTP Geofencing API-aanvragen, met verschillende corresponderende locatiecoördinaten van de apparatuur, zoals deze zijn waargenomen in chronologische volgorde. Elke aanvraag wordt gevolgd door de hoofdtekst van de reactie.
 
1. Locatie 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Geofence-query 1](./media/tutorial-geofence/geofence-query1.png)

   In bovenstaande reactie betekent de negatieve afstand ten opzichte van de hoofd-geofence dat de apparatuur zich binnen de geofence bevindt. De positieve afstand ten opzichte van de sublocatie-geofence betekent dat de apparatuur zich buiten de geofence van de sublocatie bevindt. 

2. Locatie 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Geofence-query 2](./media/tutorial-geofence/geofence-query2.png)

   Als u de voorgaande JSON-reactie zorgvuldig bekijkt, ziet u dat de apparatuur zich buiten de sublocatie maar binnen de hoofdomheining bevindt. Er wordt geen gebeurtenis geactiveerd en geen e-mailbericht verzonden.

3. Locatie 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geofence-query 3](./media/tutorial-geofence/geofence-query3.png)

   Er is een statuswijziging opgetreden en de apparatuur bevindt zich nu binnen de geofence van de hoofdlocatie en de sublocatie. Nu wordt er een gebeurtenis gepubliceerd en wordt er via e-mail een melding naar de Operations Manager verzonden.

4. Locatie 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Geofence-query 4](./media/tutorial-geofence/geofence-query4.png)

   Als u de desbetreffende reactie zorgvuldig observeert, ziet u dat er hier geen gebeurtenis wordt gepubliceerd terwijl de apparatuur de geofence van de sublocatie wel heeft verlaten. Als u de tijd bekijkt die de gebruiker in de GET-aanvraag heeft opgegeven, ziet u dat de geofence van de sublocatie is verlopen en de apparatuur zich nog steeds in de geofence van de hoofdlocatie bevindt. U ziet ook dat de geometrie-id van de geofence van de sublocatie zich bevindt onder `expiredGeofenceGeometryId` in de hoofdtekst van de reactie.


5. Locatie 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geofence-query 5](./media/tutorial-geofence/geofence-query5.png)

   U kunt zien dat de apparatuur de geofence van de hoofdbouwplaats heeft verlaten. Hiermee wordt een gebeurtenis gepubliceerd; het is een ernstige schending en er wordt een e-mail met een kritieke waarschuwing verzonden naar de Operations Manager.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een geofence instelt door deze met de Data Upload-API te uploaden in de gegevensservice van Azure Maps. U hebt ook geleerd hoe u Events Grid van Azure Maps gebruikt om u te abonneren op geofence-gebeurtenissen en deze af te handelen. 

* Zie [inhoudstypen in Azure Logic Apps verwerken](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type) voor informatie over hoe u Logic Apps gebruikt om JSON te parseren voor het bouwen van een complexere logica.
* Zie [Event handlers in Azure Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers) (Gebeurtenis-handlers in Azure Event Grid) voor meer informatie over gebeurtenis-handlers in Event Grid.

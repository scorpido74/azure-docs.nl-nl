---
title: Reageren op kaartgebeurtenissen met eventgrid | Microsoft Azure Maps
description: In dit artikel leert u hoe u reageren op Microsoft Azure Maps-gebeurtenissen met behulp van Gebeurtenisraster.
author: philmea
ms.author: philmea
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9c9483af191e5439af0c0b5e433187d6475c178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335716"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reageren op Azure Maps-gebeurtenissen met gebeurtenisraster 

Azure Maps integreert met Azure Event Grid, zodat gebruikers gebeurtenismeldingen naar andere services kunnen verzenden en downstreamprocessen kunnen activeren. Het doel van dit artikel is om u te helpen bij het configureren van uw bedrijfstoepassingen om naar Azure Maps-gebeurtenissen te luisteren. Hierdoor kunnen gebruikers op een betrouwbare, schaalbare en veilige manier reageren op kritieke gebeurtenissen. Gebruikers kunnen bijvoorbeeld een toepassing bouwen om een database bij te werken, een ticket te maken en een e-mailmelding te geven, telkens wanneer een apparaat een geofence binnenkomt.

Azure Event Grid is een volledig beheerde gebeurtenisrouteringsservice, die gebruikmaakt van een publicatie-abonnementmodel. Event Grid heeft ingebouwde ondersteuning voor Azure-services zoals [Azure-functies](https://docs.microsoft.com/azure/azure-functions/functions-overview) en [Azure Logic Apps.](https://docs.microsoft.com/azure/azure-functions/functions-overview) Het kan gebeurteniswaarschuwingen leveren aan niet-Azure-services met behulp van webhooks. Zie [Een inleiding tot Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview)voor een volledige lijst met de gebeurtenishandlers die Event Grid ondersteunt.


![Multifunctioneel model Azure Event Grid](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Gebeurtenissenin Azure Maps

Gebeurtenisraster gebruikt [gebeurtenisabonnementen](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) om gebeurtenisberichten door te sturen naar abonnees. Een Azure Maps-account zendt de volgende gebeurtenistypen uit: 

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Verhoogd wanneer ontvangen coördinaten zijn verplaatst van buiten een bepaalde geofence naar binnen |
| Microsoft.Maps.GeofenceExited | Verhoogd wanneer ontvangen coördinaten zijn verplaatst van binnen een bepaalde geofence naar buiten |
| Microsoft.Maps.GeofenceResultaat | Elke keer dat een geofencingquery een resultaat retourneert, ongeacht de status |

## <a name="event-schema"></a>Gebeurtenisschema

In het volgende voorbeeld wordt het schema voor GeofenceResult weergegeven:

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

## <a name="tips-for-consuming-events"></a>Tips voor het consumeren van gebeurtenissen

Toepassingen die geofence-gebeurtenissen in Azure Maps verwerken, moeten een aantal aanbevolen procedures volgen:

* Configureer meerdere abonnementen om gebeurtenissen naar dezelfde gebeurtenishandler te leiden. Het is belangrijk om niet aan te nemen dat gebeurtenissen van een bepaalde bron zijn. Controleer altijd het berichtonderwerp om ervoor te zorgen dat het bericht afkomstig is van de bron die u verwacht.
* Gebruik `X-Correlation-id` het veld in de antwoordkop om te begrijpen of uw informatie over objecten up-to-date is. Berichten kunnen buiten de orde komen of na een vertraging.
* Wanneer een GET- of een POST-aanvraag in de Geofence-API wordt aangeroepen met de modusparameter ingesteld op `EnterAndExit`, wordt een gebeurtenis Enter of Exit gegenereerd voor elke geometrie in de geofence waarvoor de status is gewijzigd ten opzichte van de vorige Geofence API-aanroep.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het gebruik van geofencing om bewerkingen op een bouwplaats te beheren:

> [!div class="nextstepaction"] 
> [Een geofence instellen met behulp van Azure Maps](tutorial-geofence.md)

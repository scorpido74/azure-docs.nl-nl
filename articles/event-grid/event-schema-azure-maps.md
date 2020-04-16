---
title: Azure Maps als gebeurtenisrasterbron
description: Beschrijft de eigenschappen en het schema die zijn opgegeven voor Azure Maps-gebeurtenissen met Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: e879ec3442f2e7912acb450a97079d80d7d95a01
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393406"
---
# <a name="azure-maps-as-an-event-grid-source"></a>Azure Maps als gebeurtenisrasterbron

In dit artikel vindt u de eigenschappen en het schema voor Azure Maps-gebeurtenissen. Zie Azure Event Grid-gebeurtenisschema voor een inleiding tot gebeurtenisschema ['Azure Event Grid'.](https://docs.microsoft.com/azure/event-grid/event-schema) Het geeft u ook een lijst met snelle starts en zelfstudies om Azure Maps als gebeurtenisbron te gebruiken.

## <a name="event-grid-event-schema"></a>Gebeurtenisschema gebeurtenisraster

### <a name="available-event-types"></a>Beschikbare gebeurtenistypen

Een Azure Maps-account zendt de volgende gebeurtenistypen uit:

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Verhoogd wanneer de ontvangen coördinaten van buiten een bepaalde geofence naar binnen |
| Microsoft.Maps.GeofenceExited | Verhoogd wanneer de ontvangen coördinaten zijn verplaatst van binnen een bepaalde geofence naar buiten |
| Microsoft.Maps.GeofenceResultaat | Elke keer dat een geofencingquery een resultaat retourneert, ongeacht de status |

### <a name="event-examples"></a>Voorbeelden van gebeurtenissen

In het volgende voorbeeld wordt het schema van een **gebeurtenis GeofenceEntered weergegeven**

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceEntered", 
   "eventTime":"2018-11-08T00:54:17.6408601Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

In het volgende voorbeeld wordt het schema voor **GeofenceResult weergegeven** 

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

### <a name="event-properties"></a>Gebeurtenis-eigenschappen

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| onderwerp | tekenreeks | Volledig resourcepad naar de gebeurtenisbron. Dit veld is niet schrijfbaar. Event Grid biedt deze waarde. |
| Onderwerp | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| eventType | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| eventTime | tekenreeks | De tijd dat de gebeurtenis wordt gegenereerd op basis van de UTC-tijd van de provider. |
| id | tekenreeks | Unieke id voor de gebeurtenis. |
| data | object | Geofencing-gebeurtenisgegevens. |
| dataVersion | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| apiCategorie | tekenreeks | API-categorie van de gebeurtenis. |
| apiName | tekenreeks | API-naam van de gebeurtenis. |
| issues | object | Lijsten problemen ondervonden tijdens de verwerking. Als er problemen worden geretourneerd, worden er geen geometrieën geretourneerd met het antwoord. |
| responseCode | getal | HTTP-antwoordcode |
| Geometrieën | object | Hiermee worden de afrasteringsgeometrieën weergegeven die de coördinatenpositie bevatten of de zoekbuffer rond de positie overlappen. |

Het foutobject wordt geretourneerd wanneer er een fout optreedt in de Api kaarten. Het foutobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| error | ErrorDetails |Dit object wordt geretourneerd wanneer er een fout optreedt in de Api kaarten  |

Het object ErrorDetails wordt geretourneerd wanneer er een fout optreedt in de Api kaarten. De Foutdetails of het object heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| code | tekenreeks | De HTTP-statuscode. |
| message | tekenreeks | Indien beschikbaar, een menselijke leesbare beschrijving van de fout. |
| innererror | Innererror | Indien beschikbaar, een object dat servicespecifieke informatie over de fout bevat. |

De InnerError is een object dat servicespecifieke informatie over de fout bevat. Het object InnerError heeft de volgende eigenschappen: 

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| code | tekenreeks | De foutmelding. |

Het object geometrie bevat geometrie-id's van de geofences die zijn verlopen ten opzichte van de gebruikerstijd in de aanvraag. Het object geometrie heeft geometrie-items met de volgende eigenschappen: 

| Eigenschap | Type | Beschrijving |
|:-------- |:---- |:----------- |
| deviceid | tekenreeks | ID van het apparaat. |
| afstand | tekenreeks | <p>Afstand van de coördinaat tot de dichtstbijzijnde rand van de geofence. Positief betekent dat de coördinaat buiten de geofence is. Als de coördinaat zich buiten de geofence bevindt, maar meer dan de waarde van searchBuffer verwijderd van de dichtstbijzijnde geofence-grens, dan is de waarde 999. Negatief betekent dat de coördinaat zich binnen in de geofence bevindt. Als de coördinaat zich binnen de veelhoek bevindt, maar meer dan de waarde van searchBuffer verwijderd van de dichtstbijzijnde geofencing-grens, dan is de waarde -999. Een waarde van 999 betekent dat er veel vertrouwen is dat de coördinaat ver buiten de geofence ligt. Een waarde van -999 betekent dat er veel vertrouwen is dat de coördinaat ruim binnen de geofence valt.<p> |
| geometrie |tekenreeks | De unieke id identificeert de geofence geometrie. |
| nearestlat | getal | Breedtegraad van het dichtstbijzijnde punt van de geometrie. |
| nearestlon | getal | De longitude van het dichtstbijzijnde punt van de geometrie. |
| udId udId | tekenreeks | De unieke id die is geretourneerd van de gebruikersuploadservice bij het uploaden van een geofence. Zal niet worden opgenomen in geofencing post API. |

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| verlopenGeofenceGeometryId | tekenreeks[] | Lijsten met de geometrie-id van de geofence die is verlopen ten opzichte van de gebruikerstijd in de aanvraag. |
| Geometrieën | geometrieën[] |Hiermee worden de afrasteringsgeometrieën weergegeven die de coördinatenpositie bevatten of de zoekbuffer rond de positie overlappen. |
| ongeldigPeriodGeofenceGeometryId | tekenreeks[]  | Lijsten met de geometrie-id van de geofence die zich in een ongeldige periode bevindt ten opzichte van de gebruikerstijd in de aanvraag. |
| isEventGepubliceerd | booleaans | True als ten minste één gebeurtenis wordt gepubliceerd op de azure maps-gebeurtenisabonnee, is false als er geen gebeurtenis wordt gepubliceerd voor de azure maps-gebeurtenisabonnee. |

## <a name="tutorials-and-how-tos"></a>Zelfstudies en handleidingen
|Titel  |Beschrijving  |
|---------|---------|
| [Reageren op Azure Maps-gebeurtenissen met gebeurtenisraster](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Overzicht van de integratie van Azure Maps met Event Grid. |
| [Zelfstudie: Een geofence instellen](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | In deze zelfstudie doorloopt u de basisstappen voor het instellen van geofence met behulp van Azure Maps. U gebruikt Azure Event Grid om de geofence-resultaten te streamen en een melding in te stellen op basis van de geofence-resultaten. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is gebeurtenisraster voor](overview.md) een inleiding tot Azure Event Grid?
* Zie [Abonnement op gebeurtenisrastervoor](subscription-creation-schema.md)meer informatie over het maken van een Azure Event Grid-abonnement .
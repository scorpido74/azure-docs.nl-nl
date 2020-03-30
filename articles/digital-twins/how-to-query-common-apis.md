---
title: Algemene querypatronen - Azure Digital Twins | Microsoft Documenten
description: Lees verschillende veelvoorkomende API-querypatronen voor de Azure Digital Twins-beheer-API's.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 133c0e0dcc07afb85a0f3af9ae51d2207abac293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589110"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Azure Digital Twins API's opvragen voor veelvoorkomende taken

In dit artikel worden querypatronen weergegeven waarmee u veelvoorkomende scenario's uitvoeren voor uw Azure Digital Twins-exemplaar. Dit veronderstelt dat uw Digital Twins-exemplaar al wordt uitgevoerd. U elke REST-client gebruiken, zoals Postman. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Query's voor spaties en typen

In deze sectie worden voorbeeldquery's weergegeven om meer informatie te krijgen over uw ingerichte ruimten. Maak geverifieerde HTTP-aanvragen ophalen met de voorbeeldquery's en vervang de tijdelijke aanduidingen door waarden uit uw instelling. 

- Krijg spaties die rootknooppunten zijn.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Krijg een ruimte op naam en omvatten apparaten, sensoren, berekende waarden en sensorwaarden. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Krijg spaties en hun apparaat / sensor informatie, waarvan de ouder is de gegeven ruimte-ID, en die op niveaus twee tot vijf [ten opzichte van de gegeven ruimte](how-to-navigate-apis.md#api-navigation). 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- Krijg de ruimte met de opgegeven ID en neem berekende en sensorwaarden op.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Download eigenschapssleutels voor een bepaalde ruimte.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Krijg spaties met de eigenschapsleutel met de naam *AreaInSqMeters* en de waarde ervan is 30. U ook tekenreeksbewerkingen uitvoeren, bijvoorbeeld spaties `name = X contains Y`met een eigenschapssleutel met.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Download alle namen met *naamTemperatuur* en bijbehorende afhankelijkheden en ontologieën.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Query's voor rollen en roltoewijzingen

In deze sectie worden enkele query's weergegeven om meer informatie te krijgen over rollen en hun toewijzingen. 

- Krijg alle rollen ondersteund door Azure Digital Twins.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Krijg alle rolopdrachten in je Digital Twins-exemplaar. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Krijg roltoewijzingen op een bepaald pad.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Query's voor apparaten

In dit gedeelte worden enkele voorbeelden weergegeven van hoe u de beheer-API's gebruiken om specifieke informatie over uw apparaten te krijgen. Alle API-aanroepen moeten worden geverifieerd GET HTTP-aanvragen.

- Haal alle apparaten.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Zoek alle apparaatstatussen.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Neem een specifiek apparaat.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Zorg ervoor dat alle apparaten aan de hoofdruimte zijn gekoppeld.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Zorg ervoor dat alle apparaten zijn gekoppeld aan ruimten op niveaus 2 tot en met 4.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Zorg ervoor dat alle apparaten direct zijn gekoppeld aan een bepaalde ruimte-ID.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Zorg ervoor dat alle apparaten zijn gekoppeld aan een bepaalde ruimte en de afstammelingen.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Zorg ervoor dat alle apparaten zijn gekoppeld aan afstammelingen van een ruimte, met uitzondering van die ruimte.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Zorg ervoor dat alle apparaten zijn gekoppeld aan directe kinderen van een ruimte.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Zorg ervoor dat alle apparaten zijn gekoppeld aan een van de voorouders van een ruimte.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Zorg ervoor dat alle apparaten zijn gekoppeld aan afstammelingen van een ruimte die kleiner is dan of gelijk is aan 5.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Zorg ervoor dat alle apparaten zijn gekoppeld aan ruimten die op hetzelfde niveau zijn als de ruimte met *ID-YOUR_SPACE_ID.*

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Download de verbindingstekenreeks van het IoT Hub-apparaat voor uw apparaat.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Krijg het apparaat met de opgegeven hardware-ID, inclusief aangesloten sensoren.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Krijg sensoren voor bepaalde gegevenstypen, in dit geval *Beweging* en *Temperatuur.*

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Query's voor matchers en door de gebruiker gedefinieerde functies 

- Krijg alle ingerichte matchers en hun id's.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Informatie over een bepaalde matcher, inclusief de spaties en de door de gebruiker gedefinieerde functie die eraan is gekoppeld.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Evalueer een matcher tegen een sensor en schakel logboekregistratie in voor foutopsporingsdoeleinden. De terugkeer van dit HTTP GET-bericht geeft aan of de matcher en de sensor tot het gegevenstype behoren. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- Krijg de id van de door de gebruiker gedefinieerde functies. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- De inhoud van een bepaalde door de gebruiker gedefinieerde functie krijgen 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Query's voor gebruikers

In deze sectie worden enkele voorbeeld-API-query's weergegeven voor het beheren van gebruikers in Azure Digital Twins. Maak een HTTP GET-aanvraag om de tijdelijke aanduidingen te vervangen door waarden uit uw installatie. 

- Krijg alle gebruikers. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Haal een specifieke gebruiker.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Volgende stappen

Lees [Authenticeren met API's](./security-authenticating-apis.md)voor meer informatie over hoe u zich verifiëren met uw Beheer-API.

Lees Voor meer informatie over uw API-eindpunten [Hoe u Digital Twins Swagger gebruikt.](./how-to-use-swagger.md)

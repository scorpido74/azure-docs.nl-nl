---
title: Algemene query patronen voor Azure Digital Apparaatdubbels | Microsoft Docs
description: Meer informatie over algemene patronen voor het uitvoeren van query's in de Azure Digital Apparaatdubbels Management-Api's.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 1d1c0e2f12d5a971cf08933e5d469093eb1f6d52
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949880"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Query's uitvoeren op Azure Digital Apparaatdubbels-Api's voor algemene taken

In dit artikel vindt u query patronen die u helpen bij het uitvoeren van algemene scenario's voor uw Azure Digital Apparaatdubbels-exemplaar. Hierbij wordt ervan uitgegaan dat uw Digital Apparaatdubbels-exemplaar al wordt uitgevoerd. U kunt elke REST-client gebruiken, zoals een postman. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Query's voor spaties en typen

In deze sectie worden voorbeeld query's weer gegeven om meer informatie over uw ingerichte ruimten te krijgen. Zorg ervoor dat geverifieerde HTTP-aanvragen worden ontvangen met de voorbeeld query's, waarbij de tijdelijke aanduidingen worden vervangen door waarden van uw instellingen. 

- Haal ruimten op die hoofd knooppunten zijn.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Een ruimte op naam ophalen en apparaten, Sens oren, berekende waarden en sensor waarden bevatten. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Haal ruimten en hun apparaat/sensor gegevens op, waarvan het bovenliggende item de opgegeven ruimte-ID is en die zich op niveau twee tot vijf [relatief ten opzichte van de opgegeven ruimte](how-to-navigate-apis.md#api-navigation)bevinden. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- De ruimte met de opgegeven ID ophalen en waarden voor berekende en sensors bevatten.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Eigenschappen sleutels voor een bepaalde ruimte ophalen.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- U kunt spaties ophalen met de eigenschaps sleutel met de naam *AreaInSqMeters* en de bijbehorende waarde is 30. U kunt ook teken reeks bewerkingen uitvoeren, bijvoorbeeld spaties met de eigenschaps sleutel met `name = X contains Y` ophalen.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Alle namen ophalen met naam *temperatuur* en gekoppelde afhankelijkheden en Ontologies.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Query's voor rollen en roltoewijzingen

In deze sectie vindt u enkele query's voor meer informatie over rollen en hun toewijzingen. 

- Alle rollen ophalen die worden ondersteund door Azure Digital Apparaatdubbels.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Haal alle roltoewijzingen op in uw digitale Apparaatdubbels-exemplaar. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Haal roltoewijzingen op een bepaald pad op.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Query's voor apparaten

In deze sectie vindt u enkele voor beelden van hoe u de beheer-Api's kunt gebruiken om specifieke informatie over uw apparaten te verkrijgen. Alle API-aanroepen moeten geverifieerde HTTP-aanvragen ontvangen.

- Alle apparaten ophalen.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Alle Apparaatstatus zoeken.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Een specifiek apparaat ophalen.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Alle apparaten ophalen die aan de hoofd ruimte zijn gekoppeld.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Alle apparaten die zijn gekoppeld aan ruimten op niveau 2 tot en met 4 ophalen.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Alle apparaten die rechtstreeks zijn gekoppeld aan een bepaalde ruimte-ID ophalen.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Alle apparaten ophalen die zijn gekoppeld aan een bepaalde ruimte en de onderliggende objecten.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Alle apparaten ophalen die zijn gekoppeld aan descendanten van een spatie, met uitzonde ring van die spatie.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Alle apparaten ophalen die zijn gekoppeld aan directe onderliggende elementen van een ruimte.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Alle apparaten ophalen die zijn gekoppeld aan een van de bovenliggende elementen van een spatie.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Alle apparaten ophalen die zijn gekoppeld aan descendanten van een ruimte die kleiner is dan of gelijk is aan 5.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Alle apparaten ophalen die zijn gekoppeld aan ruimten die zich op hetzelfde niveau bevinden als de ruimte met de ID *YOUR_SPACE_ID*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- De IoT Hub apparaat-connection string voor uw apparaat ophalen.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Apparaat met de opgegeven hardware-ID ophalen, inclusief gekoppelde Sens oren.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Haal Sens oren op voor bepaalde gegevens typen, in dit geval *beweging* en *Tempe ratuur*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Query's voor matchers en door de gebruiker gedefinieerde functies 

- Alle ingerichte vergelijkings en hun id's ophalen.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Details over een bepaalde Matcher ophalen, inclusief de spaties en de door de gebruiker gedefinieerde functie die eraan is gekoppeld.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Evalueer een overeenkomst met een sensor en schakel logboek registratie in voor fout opsporing. Het retour neren van dit HTTP GET-bericht vertelt u of de overeenkomst en de sensor deel uitmaken van het gegevens type. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- De ID ophalen van de door de gebruiker gedefinieerde functies. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- De inhoud van een bepaalde door de gebruiker gedefinieerde functie ophalen 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Query's voor gebruikers

In deze sectie vindt u enkele voor beelden van API-query's voor het beheren van gebruikers in azure Digital Apparaatdubbels. Maak een HTTP GET-aanvraag om de tijdelijke aanduidingen te vervangen door waarden van uw instellingen. 

- Alle gebruikers ophalen. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Een specifieke gebruiker ophalen.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Volgende stappen

Lees [verificatie met api's](./security-authenticating-apis.md)voor meer informatie over het verifiëren met uw beheer-API.

Lees [hoe u Digital Apparaatdubbels Swagger kunt gebruiken](./how-to-use-swagger.md)voor meer informatie over uw API-eind punten.

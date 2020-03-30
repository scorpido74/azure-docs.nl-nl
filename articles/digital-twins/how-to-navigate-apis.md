---
title: Navigeren door de API's - Azure Digital Twins | Microsoft Documenten
description: Meer informatie over het bevragen van de Azure Digital Twins-beheer-API's.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: e9cdfd40a9672d19ef32dede0baadcdd56266bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265166"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Api's voor Azure Digital Twins-beheer gebruiken

De Azure Digital Twins-beheer-API's bieden krachtige functionaliteiten voor uw IoT-apps. In dit artikel ziet u hoe u door de API-structuur navigeert.  

## <a name="api-summary"></a>API-overzicht

De volgende lijst toont de onderdelen van de Digital Twins API's.

* [/spaties](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): Deze API's werken samen met de fysieke locaties in uw installatie. Deze helpen u bij het maken, verwijderen en beheren van de digitale toewijzingen van uw fysieke locaties in de vorm van een [ruimtelijke grafiek.](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph)

* [/apparaten](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): Deze API's werken samen met de apparaten in uw installatie. Deze apparaten kunnen een of meer sensoren beheren. Een apparaat kan bijvoorbeeld uw telefoon zijn, of een Raspberry Pi-sensorpod, of een Lora-gateway, enzovoort.

* [/sensoren](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): Deze API's helpen u te communiceren met de sensoren die zijn gekoppeld aan uw apparaten en uw fysieke locaties. De sensoren registreren en verzenden omgevingswaarden die vervolgens kunnen worden gebruikt om uw ruimtelijke omgeving te manipuleren.  

* [/resources:](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources)deze API's helpen u bij het instellen van resources, zoals een IoT-hub, voor uw Exemplaar Digital Twins.

* [/types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): Met deze API's u uitgebreide typen koppelen aan uw Digital Twins-objecten, om specifieke kenmerken aan die objecten toe te voegen. Met deze typen u objecten eenvoudig filteren en groeperen in de gebruikersinterface en de aangepaste functies die uw telemetriegegevens verwerken. Voorbeelden van uitgebreide typen zijn *DeviceType*, *SensorType*, *SensorDataType,* *SpaceType,* *SpaceSubType,* *SpaceBlobType*, *SpaceResourceType,* enzovoort.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): Deze API's helpen u bij het beheren van ontologieën, die verzamelingen van uitgebreide types zijn. Ontologieën geven namen voor objecttypen volgens de fysieke ruimte die ze vertegenwoordigen. De *BACnet-ontologie* bevat bijvoorbeeld specifieke namen voor *sensortypen*, *gegevenstypen*, *gegevenssubtypen*en *gegevenseenheidstypen*. Ontologieën worden beheerd en gemaakt door de service. Gebruikers kunnen laden en lossen oplogies. Wanneer een ontologie wordt geladen, zijn alle bijbehorende typenamen ingeschakeld en klaar om te worden ingericht in uw ruimtelijke grafiek. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): U deze API's gebruiken om aangepaste eigenschappen te maken voor uw *ruimten,* *apparaten,* *gebruikers*en *sensoren*. Deze eigenschappen worden gemaakt als sleutel-/waardeparen. U het gegevenstype voor deze eigenschappen definiëren door hun *PrimitiveDataType*in te stellen. U bijvoorbeeld een eigenschap met de naam *BasicTemperatureDeltaProcessingRefreshTime* van *typeuint* voor uw sensoren definiëren en vervolgens een waarde voor deze eigenschap toewijzen voor elk van uw sensoren. U ook beperkingen voor deze waarden toevoegen tijdens het maken van de eigenschap, zoals *Min-* en *Max-bereiken,* evenals toegestane waarden als *ValidatieGegevens*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): Met deze API's u de voorwaarden opgeven die u wilt evalueren op basis van uw binnenkomende apparaatgegevens. Zie [dit artikel](concepts-user-defined-functions.md#matchers) voor meer informatie. 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): Met deze API's u een aangepaste functie maken, verwijderen of bijwerken die wordt uitgevoerd wanneer de voorwaarden die door de *matchers* worden gedefinieerd, om gegevens uit uw installatie te verwerken. Zie [dit artikel](concepts-user-defined-functions.md#user-defined-functions) voor meer informatie over deze aangepaste functies, ook wel de door de gebruiker gedefinieerde *functies*genoemd. 

* [/eindpunten:](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints)met deze API's u eindpunten maken, zodat uw Digital Twins-oplossing kan communiceren met andere Azure-services voor gegevensopslag en -analyse. Lees [dit artikel](concepts-events-routing.md) voor meer informatie. 

* [/keyStores:](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores)Met deze API's u beveiligingssleutelwinkels voor uw ruimten beheren. Deze winkels kunnen een verzameling beveiligingssleutels bevatten en u in staat stellen om eenvoudig de nieuwste geldige sleutels op te halen.

* [/gebruikers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): Met deze API's u gebruikers koppelen aan uw ruimten, om deze personen te lokaliseren wanneer dat nodig is. 

* [/systeem](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): Met deze API's u systeembrede instellingen beheren, zoals de standaardtypen spaties en sensoren. 

* [/roleAssignments:](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments)Met deze API's u rollen koppelen aan entiteiten zoals gebruikers-id, door de gebruiker gedefinieerde functie-ID, enz. Elke roltoewijzing bevat de id van de entiteit die moet worden geassocieerd, het entiteitstype, de id van de te associëren rol, de tenant-id en een pad dat de bovengrens definieert van de resource die de entiteit met die koppeling kan openen. Lees [dit artikel](security-role-based-access-control.md) voor meer informatie.


## <a name="api-navigation"></a>API-navigatie

De Digital Twins API's ondersteunen filteren en navigeren in uw ruimtelijke grafiek met behulp van de volgende parameters:

- **spaceId**: De API filtert de resultaten door de opgegeven ruimte-ID. Bovendien is het booleaanse **vlaggebruikParentSpace** van toepassing op de /spaces-API's, wat aangeeft dat de opgegeven spatie-ID verwijst naar de bovenliggende ruimte in plaats van naar de huidige ruimte. [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) 

- **minLevel** en **maxLevel**: Root spaces worden beschouwd als op niveau 1. Spaties met bovenliggende ruimte op niveau *n* zijn op niveau *n+1*. Met deze waarden set u de resultaten filteren op specifieke niveaus. Dit zijn inclusieve waarden wanneer ingesteld. Apparaten, sensoren en andere objecten worden beschouwd als op hetzelfde niveau als hun dichtstbijzijnde ruimte. Als u alle objecten op een bepaald niveau wilt krijgen, stelt u zowel **minLevel** als **maxLevel** in op dezelfde waarde.

- **minRelative** en **maxRelative:** Wanneer deze filters worden gegeven, is het overeenkomstige niveau ten opzichte van het niveau van de opgegeven spatie-ID:
   - Relatief niveau *0* is gelijk aan het opgegeven ruimte-ID.
   - Relatief niveau *1* vertegenwoordigt spaties op hetzelfde niveau als de kinderen van de opgegeven ruimte-ID. Relatief niveau *n* vertegenwoordigt spaties lager dan de opgegeven ruimte door *n-niveaus.*
   - Relatief niveau *-1* vertegenwoordigt spaties op hetzelfde niveau als de bovenliggende ruimte van de opgegeven ruimte.

- **traverse**: Hiermee u in beide richtingen doorkruisen vanaf een bepaalde ruimte-ID, zoals opgegeven door de volgende waarden.
   - **Geen:** deze standaardwaarde filtert op de opgegeven spatie-ID.
   - **Omlaag**: Dit wordt gefiltersd door de opgegeven spatie-ID en zijn afstammelingen. 
   - **Omhoog**: Dit filtert door de gegeven ruimte-ID en zijn voorouders. 
   - **Span**: Hiermee filtert u een horizontaal gedeelte van de ruimtelijke grafiek, op hetzelfde niveau als de opgegeven ruimte-ID. Dit moet ofwel de **minRelative** of de **maxRelative** worden ingesteld waar. 


### <a name="examples"></a>Voorbeelden

In de volgende lijst worden enkele voorbeelden van navigatie via de [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) API's weergegeven. Houd er rekening `YOUR_MANAGEMENT_API_URL` mee dat de tijdelijke aanduiding verwijst `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`naar `YOUR_INSTANCE_NAME` de URI van de API's `YOUR_LOCATION` van digitale tweelingen in de indeling , waar is de naam van uw Azure Digital Twins-exemplaar en het gebied is waar uw instantie wordt gehost.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1`retourneert alle apparaten die zijn gekoppeld aan hoofdruimten.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4`retourneert alle apparaten die zijn gekoppeld aan ruimten van niveaus 2, 3 of 4.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId`retourneert alle apparaten die rechtstreeks aan mySpaceId zijn gekoppeld.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down`retourneert alle apparaten die zijn gekoppeld aan mySpaceId of een van de afstammelingen.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true`retourneert alle apparaten die zijn gekoppeld aan afstammelingen van mySpaceId, met uitzondering van mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true`retourneert alle apparaten die zijn gekoppeld aan directe kinderen van mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true`retourneert alle apparaten die zijn gekoppeld aan een van de voorouders van mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5`retourneert alle apparaten die zijn gekoppeld aan afstammelingen van mySpaceId die op een niveau kleiner zijn dan of gelijk zijn aan 5.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true`retourneert alle apparaten die zijn gekoppeld aan ruimten die zich op hetzelfde niveau bevinden als mySpaceId.


## <a name="odata-support"></a>Ondersteuning voor OData

De meeste API's die verzamelingen retourneren, zoals een GET-aanroep op /spaties, ondersteunen de volgende subset van de algemene queryopties voor [OData-systeem:](https://www.odata.org/getting-started/basic-tutorial/#queryData)  

* **$filter**
* **$orderby** 
* **$top**
* **$skip** - Als u van plan bent om de hele collectie weer te geven, moet u deze als een hele set in één gesprek aanvragen en vervolgens paging uitvoeren in uw toepassing. 

> [!NOTE]
> Sommige OData-opties (zoals queryopties **$count,** **$expand**en **$search)** worden momenteel niet ondersteund.

### <a name="examples"></a>Voorbeelden

In de volgende lijst worden verschillende query's weergegeven met geldige odata-syntaxis:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,'space')`
- `YOUR_MANAGEMENT_API_URL/devices?$filter=TypeId eq 2`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=StatusId ne 1`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,'k')&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor',Name)`
 
## <a name="next-steps"></a>Volgende stappen

Lees [Informeer Azure Digital Twins API's voor veelvoorkomende taken](./how-to-query-common-apis.md)voor meer informatie over enkele algemene API-querypatronen.

Lees Voor meer informatie over uw API-eindpunten [Hoe u Digital Twins Swagger gebruikt.](./how-to-use-swagger.md)

Als u de syntaxis en beschikbare vergelijkingsoperatoren van OData wilt bekijken, leest u [OData-vergelijkingsoperatoren in Azure Cognitive Search](../search/search-query-odata-comparison-operators.md).

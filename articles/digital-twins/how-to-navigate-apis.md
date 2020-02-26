---
title: Navigeer door de Api's-Azure Digital Apparaatdubbels | Microsoft Docs
description: Meer informatie over algemene patronen voor het uitvoeren van query's in de Azure Digital Apparaatdubbels Management-Api's.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: e9cdfd40a9672d19ef32dede0baadcdd56266bab
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589093"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Azure Digital Apparaatdubbels Management-Api's gebruiken

De Azure Digital Apparaatdubbels Management-Api's bieden krachtige functionaliteit voor uw IoT-apps. In dit artikel wordt beschreven hoe u door de API-structuur navigeert.  

## <a name="api-summary"></a>API-overzicht

De volgende lijst bevat de onderdelen van de Digital Apparaatdubbels-Api's.

* [/Spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): deze api's communiceren met de fysieke locaties in uw installatie. Deze helpen u bij het maken, verwijderen en beheren van de digitale toewijzingen van uw fysieke locaties in de vorm van een [ruimtelijke grafiek](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph).

* [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): deze api's communiceren met de apparaten in uw installatie. Deze apparaten kunnen een of meer Sens oren beheren. Een apparaat kan bijvoorbeeld uw telefoon zijn of een Raspberry Pi-sensor Pod of een Lora-gateway, enzovoort.

* [/sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): deze api's helpen u te communiceren met de Sens oren die zijn gekoppeld aan uw apparaten en uw fysieke locaties. De Sens oren record en verzenden Ambient waarden die vervolgens kunnen worden gebruikt om uw ruimtelijke omgeving te manipuleren.  

* [/resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): deze api's helpen u bij het instellen van resources, zoals een IOT-hub, voor uw digitale apparaatdubbels-instantie.

* [/types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): met deze api's kunt u uitgebreide typen koppelen aan uw digitale apparaatdubbels-objecten om specifieke kenmerken toe te voegen aan deze objecten. Met deze typen kunt u objecten eenvoudig filteren en groeperen in de gebruikers interface en de aangepaste functies die uw telemetriegegevens verwerken. Voor beelden van uitgebreide typen zijn *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType*, *SpaceBlobType*, *SpaceResourceType*, enzovoort.

* [/Ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): deze api's helpen u bij het beheren van Ontologies, die verzamelingen van uitgebreide typen zijn. Ontologies geven namen voor object typen aan volgens de fysieke ruimte die ze vertegenwoordigen. De *BACnet* -Ontology biedt bijvoorbeeld specifieke namen voor *sensor types*, *data types*, *datasubtypes*en *dataunittypes*. Ontologies worden beheerd en gemaakt door de service. Gebruikers kunnen Ontologies laden en verwijderen. Wanneer een Ontology wordt geladen, zijn alle bijbehorende type namen ingeschakeld en klaar om te worden ingericht in uw ruimtelijke grafiek. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): u kunt deze api's gebruiken om aangepaste eigenschappen te maken voor uw *Spaces*, *apparaten*, *gebruikers*en *Sens oren*. Deze eigenschappen worden gemaakt als sleutel/waarde-paren. U kunt het gegevens type voor deze eigenschappen definiëren door hun *PrimitiveDataType*in te stellen. U kunt bijvoorbeeld een eigenschap met de naam *BasicTemperatureDeltaProcessingRefreshTime* van het type *uint* definiëren voor uw Sens oren en vervolgens voor elk van uw Sens oren een waarde voor deze eigenschap toewijzen. U kunt ook beperkingen voor deze waarden toevoegen tijdens het maken van de eigenschap, zoals *min* -en *maximum aantal* bereiken, en toegestane waarden als *ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): met deze api's kunt u de voor waarden opgeven die u wilt evalueren op basis van de gegevens van uw binnenkomende apparaten. Raadpleeg [dit artikel](concepts-user-defined-functions.md#matchers) voor meer informatie. 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): met deze api's kunt u een aangepaste functie maken, verwijderen of bijwerken die wordt uitgevoerd wanneer de voor waarden die worden gedefinieerd door de *matchers* optreden, om gegevens te verwerken die afkomstig zijn van uw installatie. Raadpleeg [dit artikel](concepts-user-defined-functions.md#user-defined-functions) voor meer informatie over deze aangepaste functies, ook wel de door de *gebruiker gedefinieerde functies*genoemd. 

* [/endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): met deze api's kunt u eind punten maken zodat uw digitale apparaatdubbels-oplossing kan communiceren met andere Azure-Services voor gegevens opslag en-analyse. Lees [dit artikel](concepts-events-routing.md) voor meer informatie. 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): met deze api's kunt u beveiligings sleutel archieven voor uw Spaces beheren. Deze archieven kunnen een verzameling beveiligings sleutels bevatten en u kunt eenvoudig de meest recente geldige sleutels ophalen.

* [/Users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): met deze api's kunt u gebruikers koppelen aan uw Spaces om deze personen te vinden wanneer dat nodig is. 

* [/systeemstatus](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): met deze api's kunt u de instellingen voor het hele systeem beheren, zoals de standaard typen spaties en Sens oren. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): met deze api's kunt u rollen koppelen aan entiteiten zoals een gebruikers-id, een door de gebruiker gedefinieerde functie-id, enzovoort. Elke roltoewijzing bevat de ID van de entiteit die moet worden gekoppeld, het entiteits type, de ID van de te koppelen rol, de Tenant-ID en een pad waarmee de bovengrens van de resource wordt gedefinieerd die de entiteit kan gebruiken met die koppeling. Lees [dit artikel](security-role-based-access-control.md) voor meer informatie.


## <a name="api-navigation"></a>API-navigatie

De Digital Apparaatdubbels-Api's bieden ondersteuning voor filteren en navigatie in uw ruimtelijke grafiek met behulp van de volgende para meters:

- **spaceId**: de resultaten worden door de API gefilterd op basis van de opgegeven ruimte-id. Daarnaast is de Booleaanse vlag **useParentSpace** van toepassing op de [/Spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) -api's, wat aangeeft dat de opgegeven ruimte-id verwijst naar de bovenliggende ruimte in plaats van de huidige ruimte. 

- **minLevel** en **maxLevel**: root-ruimten worden geacht op niveau 1 te zijn. Ruimten met bovenliggende ruimte op niveau *n* zijn op niveau *n + 1*. Als deze waarden zijn ingesteld, kunt u de resultaten filteren op specifieke niveaus. Deze zijn inclusieve waarden wanneer deze zijn ingesteld. Apparaten, Sens oren en andere objecten worden geacht hetzelfde niveau te hebben als de dichtstbijzijnde ruimte. Als u alle objecten op een bepaald niveau wilt ophalen, stelt u zowel **minLevel** als **maxLevel** in op dezelfde waarde.

- **minRelative** en **maxRelative**: wanneer deze filters zijn opgegeven, is het bijbehorende niveau relatief ten opzichte van het niveau van de opgegeven ruimte-id:
   - Het relatieve niveau *0* is hetzelfde niveau als de opgegeven ruimte-id.
   - Het relatieve niveau *1* staat voor spaties op hetzelfde niveau als de onderliggende items van de opgegeven ruimte-id. Het relatieve niveau *n* staat voor spaties die kleiner zijn dan de opgegeven ruimte op het niveau van de *n* .
   - Relatief niveau *-1* staat voor spaties op hetzelfde niveau als de bovenliggende ruimte van de opgegeven ruimte.

- door gaan: Hiermee kunt u in een van de richtingen van een bepaalde ruimte-ID **door bladeren,** zoals is opgegeven met de volgende waarden.
   - **Geen**: deze standaard waarde filtert op de opgegeven ruimte-id.
   - **Omlaag**: dit filtert op basis van de opgegeven ruimte-id en de bijbehorende descendanten. 
   - **Omhoog**: Hiermee wordt gefilterd op basis van de opgegeven ruimte-id en de bijbehorende voor gaande items. 
   - **Span**: Hiermee wordt een horizon taal deel van het ruimtelijke diagram gefilterd op hetzelfde niveau als de opgegeven ruimte-id. Hiervoor moet de **minRelative** of de **maxRelative** worden ingesteld op True. 


### <a name="examples"></a>Voorbeelden

De volgende lijst bevat enkele voor beelden van navigatie via de [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) -api's. Houd er rekening mee dat de tijdelijke aanduiding `YOUR_MANAGEMENT_API_URL` verwijst naar de URI van de Digital Apparaatdubbels-Api's in de notatie `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`, waarbij `YOUR_INSTANCE_NAME` de naam van uw Azure Digital Apparaatdubbels-exemplaar is en `YOUR_LOCATION` de regio is waar uw exemplaar wordt gehost.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1` retourneert alle apparaten die zijn gekoppeld aan hoofd ruimten.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4` retourneert alle apparaten die zijn gekoppeld aan ruimten van niveau 2, 3 of 4.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId` retourneert alle apparaten die rechtstreeks zijn gekoppeld aan mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down` retourneert alle apparaten die zijn gekoppeld aan mySpaceId of een van de onderliggende objecten.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true` retourneert alle apparaten die zijn gekoppeld aan descendanten van mySpaceId, met uitzonde ring van mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true` retourneert alle apparaten die zijn gekoppeld aan directe onderliggende items van mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true` retourneert alle apparaten die zijn gekoppeld aan een van de bovenliggende items van mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5` retourneert alle apparaten die zijn gekoppeld aan descendanten van mySpaceId die kleiner zijn dan of gelijk zijn aan 5.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true` retourneert alle apparaten die zijn gekoppeld aan ruimten die zich op hetzelfde niveau bevinden als mySpaceId.


## <a name="odata-support"></a>OData-ondersteuning

De meeste Api's die verzamelingen retour neren, zoals een GET-aanroep op/Spaces, ondersteunen de volgende subset van de algemene [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) -systeem query opties:  

* **$filter**
* **$orderby** 
* **$top**
* **$Skip** : als u van plan bent om de hele verzameling weer te geven, moet u deze als een volledige set aanvragen in één aanroep en vervolgens paginering in uw toepassing uitvoeren. 

> [!NOTE]
> Sommige OData-opties (zoals query opties **$Count**, **$expand**en **$Search**) worden momenteel niet ondersteund.

### <a name="examples"></a>Voorbeelden

In de volgende lijst ziet u verschillende query's met geldige OData-syntaxis:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,'space')`
- `YOUR_MANAGEMENT_API_URL/devices?$filter=TypeId eq 2`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=StatusId ne 1`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,'k')&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor',Name)`
 
## <a name="next-steps"></a>Volgende stappen

Meer informatie over enkele veelvoorkomende API-query patronen vindt u in [het opvragen van Azure Digital apparaatdubbels-api's voor algemene taken](./how-to-query-common-apis.md).

Lees [hoe u Digital Apparaatdubbels Swagger kunt gebruiken](./how-to-use-swagger.md)voor meer informatie over uw API-eind punten.

Als u de OData-syntaxis en beschik bare vergelijkings operatoren wilt bekijken, leest u [odata-vergelijkings operatoren in Azure Cognitive Search](../search/search-query-odata-comparison-operators.md)

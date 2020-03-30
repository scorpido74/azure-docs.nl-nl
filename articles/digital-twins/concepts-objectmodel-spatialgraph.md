---
title: Objectmodellen en grafiek ruimtelijke intelligentie begrijpen - Azure Digital Twins | Microsoft Documenten
description: Azure Digital Twins gebruiken om relaties tussen personen, plaatsen en apparaten te modelleren
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: cafec321e7c40e27d8de731feda1103451271507
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265205"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Lees Digital Twins-objectmodellen en grafiek ruimtelijke intelligentie

Azure Digital Twins is een Azure IoT-service die uitgebreide virtuele weergaven van fysieke omgevingen en bijbehorende apparaten, sensoren en mensen van dienst biedt. Het verbetert de ontwikkeling door domeinspecifieke concepten te organiseren in nuttige modellen. De modellen bevinden zich dan in een ruimtelijke intelligentiegrafiek. Dergelijke concepten modelleren getrouw de relaties en interacties tussen mensen, ruimten en apparaten.

Digital Twins-objectmodellen beschrijven domeinspecifieke concepten, categorieën en eigenschappen. Modellen worden vooraf gedefinieerd door gebruikers die de oplossing willen afstemmen op hun specifieke behoeften. Samen vormen deze vooraf gedefinieerde Digital Twins objectmodellen een _ontologie._ De ontologie van een slim gebouw beschrijft regio's, locaties, vloeren, kantoren, zones, vergaderzalen en focusruimten. Een energienet ontologie beschrijft verschillende elektriciteitscentrales, onderstations, energiebronnen en klanten. Met Digital Twins object modellen en ontologieën kunnen diverse scenario's en behoeften worden aangepast.

Met Digital Twins object modellen en een ontologie op zijn plaats, u een _ruimtelijke grafiek_vullen. Ruimtelijke grafieken zijn virtuele weergaven van de vele relaties tussen ruimten, apparaten en mensen die relevant zijn voor een IoT-oplossing. Dit diagram toont een voorbeeld van een ruimtelijke grafiek die gebruik maakt van de ontologie van een slim gebouw.

[![Digitale Tweelingen ruimtegrafiekbouw](media/concepts/digital-twins-spatial-graph-building.png)](media/concepts/digital-twins-spatial-graph-building.png#lightbox)

De ruimtelijke grafiek brengt ruimtes, apparaten, sensoren en gebruikers samen. Elk is met elkaar verbonden op een manier die de echte wereld modelleert. In deze steekproef, locatie 43 heeft vier verdiepingen, elk met veel verschillende gebieden. Gebruikers worden gekoppeld aan hun werkstations en krijgen toegang tot delen van de grafiek. Een beheerder heeft het recht om wijzigingen aan te brengen in de ruimtelijke grafiek, terwijl een bezoeker het recht heeft om alleen bepaalde bouwgegevens te bekijken.

## <a name="digital-twins-object-models"></a>Digital Twins object modellen

Digital Twins object modellen ondersteunen deze hoofdcategorieën van objecten:

- **Spaties** zijn virtuele of fysieke `Tenant`locaties, `Region`bijvoorbeeld `Venue`, , `Customer`en .
- **Apparaten** zijn virtuele of fysieke apparatuur, `AwesomeCompany Device` `Raspberry Pi 3`bijvoorbeeld, en .
- **Sensoren** zijn objecten die bijvoorbeeld `AwesomeCompany Temperature Sensor` gebeurtenissen `AwesomeCompany Presence Sensor`detecteren en .
- **Gebruikers** identificeren bewoners en hun kenmerken.

Andere categorieën objecten zijn:

- **Resources** zijn gekoppeld aan een ruimte en vertegenwoordigen doorgaans Azure-resources die worden `IoTHub`gebruikt door objecten in de ruimtelijke grafiek, bijvoorbeeld .
- **Blobs** zijn gekoppeld aan objecten (zoals spaties, apparaten, sensoren en gebruikers). Ze worden gebruikt als bestanden met mimetype `maps`en `pictures`metadata, bijvoorbeeld, en `manuals`.
- **Uitgebreide typen** zijn uitbreidbare opsommingen die entiteiten met specifieke `SpaceType` kenmerken `SpaceSubtype`vergroten, bijvoorbeeld en .
- **Ontologieën vertegenwoordigen** bijvoorbeeld een reeks uitgebreide `Default`typen, `BACnet`bijvoorbeeld `EnergyGrid`, , `Building`en .
- **Eigenschappensleutels en -waarden** zijn aangepaste kenmerken van spaties, apparaten, sensoren en gebruikers. Ze kunnen worden gebruikt samen met ingebouwde `DeltaProcessingRefreshTime` kenmerken, `10` bijvoorbeeld als sleutel en als waarde.
- **Rollen** zijn sets machtigingen die zijn toegewezen aan gebruikers en `Space Administrator` `User Administrator`apparaten `Device Administrator`in de ruimtelijke grafiek, bijvoorbeeld , en .
- **Roltoewijzingen** zijn de koppeling tussen een rol en een object in de ruimtelijke grafiek. Een gebruiker of een serviceprincipal kan bijvoorbeeld toestemming krijgen om een ruimte in de ruimtelijke grafiek te beheren.
- **Beveiligingssleutelopslag** bieden de beveiligingssleutels voor alle apparaten in de hiërarchie onder een bepaald ruimteobject, zodat het apparaat veilig kan communiceren met Digital Twins.
- **Door de gebruiker gedefinieerde functies** (UDF's) maken aanpasbare sensortelemetrieverwerking in de ruimtelijke grafiek mogelijk. Een UDF kan bijvoorbeeld:
  - Stel een sensorwaarde in.
  - Voer aangepaste logica uit op basis van sensormetingen en stel de uitvoer in op een spatie.
  - Metagegevens aan een ruimte koppelen.
  - Stuur meldingen wanneer aan vooraf gedefinieerde voorwaarden is voldaan. Momenteel kunnen UDF's worden geschreven in JavaScript.
- **Matchers** zijn objecten die bepalen welke UDF's worden uitgevoerd voor een bepaald telemetriebericht.
- **Eindpunten** zijn de locaties waar telemetrieberichten en Digital Twins-gebeurtenissen `Event Hub` `Service Bus`kunnen `Event Grid`worden doorgestuurd, bijvoorbeeld , , en .

## <a name="spatial-intelligence-graph"></a>Grafiek voor ruimtelijke intelligentie

Ruimtelijke grafiek is de hiërarchische grafiek van ruimten, apparaten en personen gedefinieerd in het objectmodel Digital Twins. De ruimtelijke grafiek ondersteunt overerving, filtering, traversing, schaalbaarheid en uitbreidbaarheid. U uw ruimtelijke grafiek beheren en ermee omgaan met een verzameling REST API's.

Als u een Digital Twins-service in uw abonnement implementeert, wordt u de globale beheerder van het hoofdknooppunt. U krijgt dan automatisch volledige toegang tot de gehele structuur. Inrichten van spaties in de grafiek met behulp van de Space API. Services leveren met behulp van de Device API en sensoren met behulp van de Sensor API. [Open source-tools](https://github.com/Azure-Samples/digital-twins-samples-csharp) zijn ook beschikbaar om de grafiek in bulk in te richten.

**Graferving grafiek**. Overerving is van toepassing op de machtigingen en eigenschappen die afstammen van een bovenliggend knooppunt naar alle knooppunten eronder. Wanneer een rol bijvoorbeeld aan een gebruiker op een bepaald knooppunt wordt toegewezen, heeft de gebruiker de machtigingen van die rol voor het gegeven knooppunt en elk knooppunt eronder. Elke eigenschapsleutel en uitgebreid type gedefinieerd voor een bepaald knooppunt wordt overgenomen door alle knooppunten onder dat knooppunt.

**Grafiekfiltering**. Filteren wordt gebruikt om de resultaten van aanvragen te beperken. U filteren op id's, naam, typen, subtypen, bovenliggende ruimte en bijbehorende spaties. U ook filteren op sensorgegevenstypen, eigenschapssleutels en -waarden, *traverse,* *minLevel,* *maxLevel*en andere OData-filterparameters.

**Grafiek die doorkruisen**. U de ruimtelijke grafiek doorkruisen door zijn diepte en breedte. Voor diepte doorkruis je de grafiek boven-omlaag of onder-omhoog met behulp van de parameters *traverse,* *minLevel*en *maxLevel*. Doorkruis de grafiek om broer of zus knooppunten direct gekoppeld aan een bovenliggende ruimte of een van de afstammelingen voor de breedte. Wanneer u een object opvraagt, u alle gerelateerde objecten met betrekking tot dat object opvragen met behulp van de *parameter includes* van de GET-API's.

**Schaalbaarheid van grafieken**. Digital Twins garandeert de schaalbaarheid van grafieken, zodat het uw echte workloads aankan. Digital Twins kan worden gebruikt om grote portefeuilles van onroerend goed, infrastructuur, apparaten, sensoren, telemetrie en meer weer te geven.

**Grafiek uitbreidbaarheid**. Gebruik uitbreidbaarheid om de onderliggende Digital Twins-objectmodellen aan te passen met nieuwe typen en ontologieën. Uw Digital Twins-gegevens kunnen ook worden verrijkt met uitbreidbare eigenschappen en waarden.

### <a name="spatial-intelligence-graph-management-apis"></a>Ruimtelijke intelligentie grafiek Management API's

Nadat u Digital Twins hebt geïmplementeerd vanuit de [Azure-portal,](https://portal.azure.com)wordt de [Swagger-URL](https://swagger.io/tools/swagger-ui/) van de beheer-API's automatisch gegenereerd. Het wordt weergegeven in de Azure-portal in de sectie **Overzicht** met de volgende indeling.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Name | Vervangen door |
| --- | --- |
| YOUR_INSTANCE_NAME | De naam van uw Digital Twins-exemplaar |
| YOUR_LOCATION | De serverregio waarin het exemplaar wordt gehost |

 De volledige URL-indeling wordt in deze afbeelding weergegeven.

[![Digital Twins portal Management API](media/concepts/digital-twins-spatial-graph-management-api-url.png)](media/concepts/digital-twins-spatial-graph-management-api-url.png#lightbox)

Ga voor meer informatie over het gebruik van grafieken voor ruimtelijke intelligentie naar de sneak preview van Azure Digital Twins Management API's.

> [!TIP]
> Een Swagger sneak preview is voorzien om de API feature set aan te tonen.
> Het wordt gehost op [docs.westcentralus.azuresmartspaces.net/management/swagger.](https://docs.westcentralus.azuresmartspaces.net/management/swagger)

Meer informatie over [het gebruik van Swagger.](how-to-use-swagger.md)

Alle API-aanroepen moeten worden geverifieerd met [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). De API's volgen [microsoft rest API-richtlijnen .](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md) De meeste API's die verzamelingen retourneren, ondersteunen [OData-systeemqueryopties.](https://www.odata.org/getting-started/basic-tutorial/#queryData)

## <a name="next-steps"></a>Volgende stappen

- Lees [Azure Digital Twins-apparaatconnectiviteit en telemetrie-ingress](concepts-device-ingress.md)voor meer informatie over apparaatconnectiviteit en hoe u telemetrieberichten naar Digital Twins verzenden.

- Lees [Azure Digital Twins API-beheer en beperkingen](concepts-service-limits.md)voor meer informatie over beperkingen en beperkingen van beheer-API's.

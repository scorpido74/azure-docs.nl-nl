---
title: Meer informatie over Azure Digital Apparaatdubbels-object modellen en ruimtelijke Intelligence-grafiek | Microsoft Docs
description: Gebruik Azure Digital Twins om relaties tussen mensen, plaatsen en apparaten te modelleren
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 67a4aceb157ee3fe1b1d1553efd587a0f2838d88
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950010"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Meer informatie over Digital Apparaatdubbels-object modellen en ruimtelijke Intelligence-grafiek

Azure Digital Apparaatdubbels is een Azure IoT-service die voorziet in uitgebreide virtuele representaties van fysieke omgevingen en gekoppelde apparaten, Sens oren en personen. Het verbetert de ontwikkeling door domein-specifieke concepten te organiseren in nuttige modellen. De modellen bevinden zich vervolgens in een ruimtelijke Intelligence-grafiek. Deze concepten zijn een getrouw model van de relaties en interacties tussen personen, ruimten en apparaten.

Digital Apparaatdubbels-object modellen beschrijven domein-specifieke concepten, categorieën en eigenschappen. Modellen zijn vooraf gedefinieerd door gebruikers die de oplossing willen aanpassen aan hun specifieke behoeften. Deze vooraf gedefinieerde Digital Apparaatdubbels-object modellen vormen samen een _Ontology_. In de Ontology van een slimme build worden regio's, locaties, vloeren, kant oren, zones, Vergader ruimten en focus kamers beschreven. Een Energy grid Ontology beschrijft verschillende stroom stations, substationen, energie bronnen en klanten. Met Digital Apparaatdubbels-object modellen en-Ontologies kunnen diverse scenario's en behoeften worden aangepast.

Met Digital Apparaatdubbels-object modellen en een Ontology op locatie kunt u een _ruimtelijke grafiek_vullen. Ruimtelijke grafieken zijn virtuele voors tellingen van de vele relaties tussen ruimten, apparaten en personen die relevant zijn voor een IoT-oplossing. Dit diagram toont een voor beeld van een ruimtelijke grafiek die gebruikmaakt van de Ontology van een slimme build.

[![Digital Apparaatdubbels ruimtelijk Graph-gebouw](media/concepts/digital-twins-spatial-graph-building.png)](media/concepts/digital-twins-spatial-graph-building.png#lightbox)

Het ruimtelijke diagram brengt spaties, apparaten, Sens oren en gebruikers samen. Elk wordt aan elkaar gekoppeld op een manier die de echte wereld modelt. In dit voor beeld heeft de locatie 43 vier etages, elk met veel verschillende gebieden. Gebruikers zijn gekoppeld aan hun werk station en hebben toegang tot delen van de grafiek. Een beheerder heeft de rechten om wijzigingen aan te brengen in de ruimtelijke grafiek, terwijl een bezoeker rechten heeft om bepaalde bouw gegevens te bekijken.

## <a name="digital-twins-object-models"></a>Digital Apparaatdubbels-object modellen

Digital Apparaatdubbels-object modellen ondersteunen deze hoofd categorieën van objecten:

- **Ruimten** zijn virtuele of fysieke locaties, bijvoorbeeld `Tenant`, `Customer`, `Region` en `Venue`.
- **Apparaten** zijn virtuele machines of fysieke onderdelen, bijvoorbeeld `AwesomeCompany Device` en `Raspberry Pi 3`.
- **Sens oren** zijn objecten die gebeurtenissen detecteren, bijvoorbeeld `AwesomeCompany Temperature Sensor` en `AwesomeCompany Presence Sensor`.
- **Gebruikers** identificeren inzittenden en hun kenmerken.

Andere categorieën objecten zijn:

- **Resources** zijn gekoppeld aan een ruimte en vertegenwoordigen doorgaans Azure-resources die worden gebruikt door objecten in de ruimtelijke grafiek, bijvoorbeeld `IoTHub`.
- **Blobs** zijn gekoppeld aan objecten (zoals spaties, apparaten, Sens oren en gebruikers). Ze worden gebruikt als bestanden met MIME-type en meta gegevens, bijvoorbeeld `maps`, `pictures` en `manuals`.
- **Uitgebreide typen** zijn uitbreid bare opsommingen die entiteiten uitbreiden met specifieke kenmerken, bijvoorbeeld `SpaceType` en `SpaceSubtype`.
- **Ontologies** vertegenwoordigen een set uitgebreide typen, bijvoorbeeld `Default`, `Building`, `BACnet` en `EnergyGrid`.
- **Eigenschaps sleutels en-waarden** zijn aangepaste kenmerken van ruimten, apparaten, Sens oren en gebruikers. Ze kunnen worden gebruikt in combi natie met ingebouwde kenmerken, bijvoorbeeld `DeltaProcessingRefreshTime` als sleutel en `10` als waarde.
- **Rollen** zijn sets machtigingen die zijn toegewezen aan gebruikers en apparaten in de ruimtelijke grafiek, bijvoorbeeld `Space Administrator`, `User Administrator` en `Device Administrator`.
- **Roltoewijzingen** zijn de koppeling tussen een rol en een object in de ruimtelijke grafiek. U kunt bijvoorbeeld een gebruiker of een Service-Principal machtigen voor het beheren van een ruimte in het ruimtelijke diagram.
- **Beveiligings sleutel archieven** bieden de beveiligings sleutels voor alle apparaten in de hiërarchie onder een bepaald ruimte-object, zodat het apparaat veilig kan communiceren met Digital apparaatdubbels.
- Met door de **gebruiker gedefinieerde functies** (udf's) kunnen aanpas bare Sens-metrie-verwerking binnen de ruimtelijke grafiek worden toegestaan. Een UDF kan bijvoorbeeld:
  - Stel een sensor waarde in.
  - Voer aangepaste logica uit op basis van sensor-Lees bewerkingen en stel de uitvoer in op een spatie.
  - Meta gegevens koppelen aan een ruimte.
  - Meldingen verzenden wanneer wordt voldaan aan vooraf gedefinieerde voor waarden. Op dit moment kunnen Udf's worden geschreven in Java script.
- **Matchers** zijn objecten die bepalen welke udf's worden uitgevoerd voor een gegeven telemetrie-bericht.
- **Eind punten** zijn de locaties waar telemetrie-berichten en digitale apparaatdubbels-gebeurtenissen kunnen worden gerouteerd, bijvoorbeeld `Event Hub`, `Service Bus` en `Event Grid`.

## <a name="spatial-intelligence-graph"></a>Grafiek voor ruimtelijke intelligentie

Ruimtelijke grafiek is het hiërarchische diagram van ruimten, apparaten en personen die in het object model Digital Apparaatdubbels zijn gedefinieerd. De ruimtelijke grafiek ondersteunt overname, filteren, door lopen, schalen en uitbreiden. U kunt uw ruimtelijke grafiek beheren en gebruiken met een verzameling REST-Api's.

Als u een Digital Apparaatdubbels-service in uw abonnement implementeert, wordt u de globale beheerder van het hoofd knooppunt. Vervolgens krijgt u automatisch volledige toegang tot de volledige structuur. Ruimten in de grafiek inrichten met behulp van de Space-API. Services inrichten met behulp van de API en Sens oren van het apparaat met behulp van de sensor-API. [Open-source-hulpprogram ma's](https://github.com/Azure-Samples/digital-twins-samples-csharp) zijn ook beschikbaar om de grafiek in bulk in te richten.

De **grafiek overname**. Overname is van toepassing op de machtigingen en eigenschappen die aflopend van een bovenliggend knoop punt naar alle onderliggende knoop punten. Wanneer een rol bijvoorbeeld wordt toegewezen aan een gebruiker op een bepaald knoop punt, heeft de gebruiker die rol machtigingen voor het opgegeven knoop punt en elk knoop punt daaronder. Elke eigenschaps sleutel en een uitgebreid type die voor een bepaald knoop punt zijn gedefinieerd, worden overgenomen door alle knoop punten onder dat knoop punt.

**Filteren van grafieken**. Filteren wordt gebruikt om de aanvraag resultaten te beperken. U kunt filteren op Id's, naam, typen, subtypen, bovenliggende ruimte en gekoppelde spaties. U kunt ook filteren op sensor gegevens typen, eigenschaps sleutels en waarden, *Bladeren*, *minLevel*, *maxLevel*en andere OData-filter parameters.

De **grafiek wordt gepasseerd**. U kunt de ruimtelijke grafiek passeren door de diepte en breedte. Voor diepte kunt u de grafiek in de bovenste of onderste naar beneden bladeren met behulp van de para meters *Traverse*, *minLevel*en *maxLevel*. Blader door de grafiek om alle knoop punten op hetzelfde niveau rechtstreeks aan een bovenliggende ruimte of een van de afgeleide elementen voor breedte op te halen. Wanneer u een query op een object uitvoert, kunt u alle verwante objecten ophalen die relaties met het object hebben met behulp van de para meter *includes* van de Get-api's.

**Schaal baarheid van grafieken**. Met Digital Apparaatdubbels wordt de schaal baarheid van grafieken gegarandeerd, zodat deze uw reële werk belastingen kan verwerken. Digital Apparaatdubbels kan worden gebruikt om grote portefeuilles van onroerend goed, infra structuur, apparaten, Sens oren, telemetrie en meer weer te geven.

**Grafiek uitbreid baarheid**. Gebruik uitbreid baarheid om de onderliggende Digital Apparaatdubbels-object modellen aan te passen met nieuwe typen en Ontologies. Uw digitale Apparaatdubbels gegevens kunnen ook worden verrijkt met uitbreid bare eigenschappen en waarden.

### <a name="spatial-intelligence-graph-management-apis"></a>Beheer-Api's van ruimtelijk Intelligence-grafiek

Nadat u Digital Apparaatdubbels hebt geïmplementeerd vanuit de [Azure Portal](https://portal.azure.com), wordt de [Swagger](https://swagger.io/tools/swagger-ui/) -URL van de beheer-api's automatisch gegenereerd. Deze wordt weer gegeven in de Azure Portal in de sectie **overzicht** met de volgende indeling.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Name | Vervangen door |
| --- | --- |
| YOUR_INSTANCE_NAME | De naam van uw Digital Twins-exemplaar |
| YOUR_LOCATION | De serverregio waarin het exemplaar wordt gehost |

 De volledige URL-indeling wordt weer gegeven in deze afbeelding.

(![Digital Apparaatdubbels Portal Management API](media/concepts/digital-twins-spatial-graph-management-api-url.png)] (Media/concepten/Digital-Twins-Spatial-Graph-Management-API-URL. png # Lightbox)

Ga naar de preview-versie van Azure Digital Apparaatdubbels Management Api's alvast voor meer informatie over het gebruik van ruimtelijke Intelligence-grafieken.

> [!TIP]
> Er wordt een Swagger alvast preview opgegeven om de API-functieset te demonstreren.
> Het wordt gehost op [docs.westcentralus.azuresmartspaces.net/Management/Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Meer informatie over [het gebruik van Swagger](how-to-use-swagger.md).

Alle API-aanroepen moeten worden geverifieerd met behulp van [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). De Api's volgen de [richt lijnen conventies voor micro soft rest API](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). De meeste Api's die verzamelingen retour neren ondersteunen opties voor [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) -systeem query's.

## <a name="next-steps"></a>Volgende stappen

- Lees voor meer informatie over de connectiviteit van apparaten en het verzenden van telemetrie-berichten naar Digital Apparaatdubbels de [Azure Digital apparaatdubbels-verbinding en inkomend telemetrie-apparaten](concepts-device-ingress.md).

- Lees [Azure Digital APPARAATDUBBELS API management en beperkingen](concepts-service-limits.md)voor meer informatie over beperkingen en beperkingen voor beheer-api's.

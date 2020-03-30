---
title: Gegevensverwerking en door de gebruiker gedefinieerde functies - Azure Digital Twins| Microsoft Documenten
description: Overzicht van gegevensverwerking, matchers en door de gebruiker gedefinieerde functies met Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 75ed2029582438ede43687addfd54c0a187e0120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265179"
---
# <a name="data-processing-and-user-defined-functions"></a>Gegevensverwerking en door gebruikers gedefinieerde functies

Azure Digital Twins biedt geavanceerde rekenmogelijkheden. Ontwikkelaars kunnen aangepaste functies definiëren en uitvoeren ten opzichte van binnenkomende telemetrieberichten om gebeurtenissen naar vooraf gedefinieerde eindpunten te verzenden.

## <a name="data-processing-flow"></a>Gegevensverwerkingsstroom

Nadat apparaten telemetriegegevens naar Azure Digital Twins hebben verzonden, kunnen ontwikkelaars gegevens in vier fasen *verwerken: valideren, matchen,* *berekenen*en *verzenden*. *match*

[![Azure Digital Twins-gegevensverwerkingsstroom](media/concepts/digital-twins-data-processing-flow.png)](media/concepts/digital-twins-data-processing-flow.png#lightbox)

1. De validatiefase transformeert het binnenkomende telemetriebericht naar een algemeen begrepen [objectindeling voor gegevensoverdracht.](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5) Deze fase voert ook apparaat- en sensorvalidatie uit.
1. In de overeenkomstfase worden de juiste door de gebruiker gedefinieerde functies gevonden die moeten worden uitgevoerd. Vooraf gedefinieerde matchers vinden de door de gebruiker gedefinieerde functies op basis van het apparaat, de sensor en de ruimte-informatie uit het binnenkomende telemetriebericht.
1. In de rekenfase worden de door de gebruiker gedefinieerde functies uitgevoerd die in de vorige fase zijn afgestemd. Deze functies kunnen berekende waarden op ruimtelijke grafiekknooppunten lezen en bijwerken en kunnen aangepaste meldingen uitzenden.
1. De verzendfase leidt alle aangepaste meldingen van de compute-fase naar eindpunten die in de grafiek zijn gedefinieerd.

## <a name="data-processing-objects"></a>Gegevensverwerkingsobjecten

Gegevensverwerking in Azure Digital Twins bestaat uit het definiëren van drie objecten: *matchers,* *door de gebruiker gedefinieerde functies*en *roltoewijzingen*.

[![Azure Digital Twins-gegevensverwerkingsobjecten](media/concepts/digital-twins-user-defined-functions.png)](media/concepts/digital-twins-user-defined-functions.png#lightbox)

### <a name="matchers"></a>Matchers

Matchers definiëren een set voorwaarden die evalueren welke acties plaatsvinden op basis van inkomende sensortelemetrie. Voorwaarden om de overeenkomst te bepalen kunnen eigenschappen van de sensor, het bovenliggende apparaat van de sensor en de bovenliggende ruimte van de sensor omvatten. De voorwaarden worden uitgedrukt als vergelijkingen met een [JSON-pad](https://jsonpath.com/) zoals beschreven in dit voorbeeld:

- Alle sensoren van datatype **Temperatuur** die door de ontsnapte waarde van de Koord wordt vertegenwoordigd`\"Temperature\"`
- Het `01` hebben in hun haven
- Welke tot apparaten met de uitgebreide bezitssleutel behoren **Fabrikant** die aan de ontsnapte waarde van de Koord wordt geplaatst`\"Contoso\"`
- Die behoren tot ruimten van het type dat door de ontsnapte tekenreeks is opgegeven`\"Venue\"`
- Wat afstammelingen zijn van ouder **SpaceId**`DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

```JSON
{
  "id": "23535afafd-f39b-46c0-9b0c-0dd3892a1c30",
  "name": "My custom matcher",
  "spaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value Contoso and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "conditions": [
    {
      "id": "43898sg43-e15a-4e9c-abb8-2gw464364",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "id": "wt3th44-e15a-35sg-seg3-235wf3ga463",
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "id": "735hs33-e15a-37jj-23532-db901d550af5",
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"Contoso\"",
      "comparison": "Equals"
    },
    {
      "id": "222325-e15a-49fg-5744-463643644",
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - JSON-paden zijn hoofdlettergevoelig.
> - De JSON payload is hetzelfde als de payload die wordt geretourneerd door:
>   - `/sensors/{id}?includes=properties,types`voor de sensor.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes`voor het bovenliggende apparaat van de sensor.
>   - `/spaces/{id}?includes=properties,types,location,timezone`voor de ouderruimte van de sensor.
> - De vergelijkingen zijn geval ongevoelig.

### <a name="user-defined-functions"></a>Door de gebruiker gedefinieerde functies

Een door de gebruiker gedefinieerde functie is een aangepaste functie die wordt uitgevoerd binnen een geïsoleerde Azure Digital Twins-omgeving. Door de gebruiker gedefinieerde functies hebben toegang tot raw sensor telemetrie bericht als het wordt ontvangen. Door de gebruiker gedefinieerde functies hebben ook toegang tot de ruimtelijke grafiek en dispatcherservice. Nadat de door de gebruiker gedefinieerde functie in een grafiek is geregistreerd, moet een matcher [(hierboven](#matchers)beschreven) worden gemaakt om aan te geven wanneer de functie wordt uitgevoerd. Wanneer Azure Digital Twins bijvoorbeeld nieuwe telemetrie van een bepaalde sensor ontvangt, kan de overeenkomende door de gebruiker gedefinieerde functie een voortschrijdend gemiddelde van de laatste paar sensormetingen berekenen.

Door de gebruiker gedefinieerde functies kunnen worden geschreven in JavaScript. Helpermethoden werken samen met de grafiek in de door de gebruiker gedefinieerde uitvoeringsomgeving. Ontwikkelaars kunnen aangepaste codefragmenten uitvoeren tegen sensortelemetrieberichten. Voorbeelden zijn:

- Stel de sensormeting rechtstreeks in op het sensorobject in de grafiek.
- Voer een actie uit op basis van verschillende sensormetingen in een ruimte in de grafiek.
- Maak een melding wanneer aan bepaalde voorwaarden is voldaan voor het lezen van een binnenkomende sensor.
- Voeg grafiekmetagegevens toe aan de sensormeting voordat u een melding verzendt.

Lees voor meer informatie [Hoe gebruik je door de gebruiker gedefinieerde functies](./how-to-user-defined-functions.md).

#### <a name="examples"></a>Voorbeelden

De [GitHub repo voor het voorbeeld Digital Twins C#](https://github.com/Azure-Samples/digital-twins-samples-csharp/) bevat een paar voorbeelden van de door de gebruiker gedefinieerde functies:
- [Deze functie](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) zoekt naar kooldioxide, beweging en temperatuurwaarden om te bepalen of een ruimte beschikbaar is met deze waarden binnen bereik. De [tutorials voor Digital Twins](tutorial-facilities-udf.md) verkennen deze functie in meer details. 
- [Deze functie](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js) zoekt naar gegevens van meerdere bewegingssensoren en bepaalt dat de ruimte beschikbaar is als geen van hen enige beweging detecteert. U eenvoudig de door de gebruiker gedefinieerde functie vervangen die wordt gebruikt in de [quickstart](quickstart-view-occupancy-dotnet.md)of de [zelfstudies](tutorial-facilities-setup.md)door de wijzigingen aan te brengen die in het opmerkingengedeelte van het bestand worden vermeld. 

### <a name="role-assignment"></a>Nieuwe roltoewijzing

De acties van een door de gebruiker gedefinieerde functie zijn onderworpen aan azure digital [twins-toegangsbeheer op basis van een rol](./security-role-based-access-control.md) om gegevens binnen de service te beveiligen. Roltoewijzingen bepalen welke door de gebruiker gedefinieerde functies de juiste machtigingen hebben om te communiceren met de ruimtelijke grafiek en de entiteiten ervan. Een door de gebruiker gedefinieerde functie kan bijvoorbeeld de mogelijkheid en toestemming hebben om *grafiekgegevens onder*een bepaalde ruimte te maken, *TE LEZEN,* *BIJWERKEN*of *te verwijderen.* Het toegangsniveau van een door de gebruiker gedefinieerde functie wordt gecontroleerd wanneer de door de gebruiker gedefinieerde functie de grafiek om gegevens vraagt of een actie probeert. Lees voor meer informatie [Toegangsbeheer op basis van rollen](./security-create-manage-role-assignments.md).

Het is mogelijk voor een matcher om een door de gebruiker gedefinieerde functie te activeren die geen roltoewijzingen heeft. In dit geval kan de door de gebruiker gedefinieerde functie geen gegevens uit de grafiek lezen.

## <a name="next-steps"></a>Volgende stappen

- Lees [Route-gebeurtenissen en -berichten](./concepts-events-routing.md)voor meer informatie over het routeren van gebeurtenissen en telemetrieberichten naar andere Azure-services.

- Lees [Handleiding voor het gebruik van door de gebruiker gedefinieerde functies voor](./how-to-user-defined-functions.md)het maken van matchers, door de gebruiker gedefinieerde functies.

- Controleer de [door de gebruiker gedefinieerde referentiedocumentatie voor functieclientbibliotheek](./reference-user-defined-functions-client-library.md).

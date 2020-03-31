---
title: Azure Cosmos DB Gremlin-antwoordkoppen
description: Referentiedocumentatie voor metagegevens voor serverrespons waarmee extra probleemoplossing mogelijk is
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 95677f4c45c0213de5ffac5521bac1c6bf7294e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72755084"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Azure Cosmos DB Gremlin-serverresponskoppen
In dit artikel worden headers behandeld die de Cosmos DB Gremlin-server retourneert naar de aanroeper bij het uitvoeren van de aanvraag. Deze headers zijn handig voor het oplossen van problemen met de prestaties van aanvragen, het bouwen van een toepassing die zonder benodigde systeemaanpassingen kan worden geïntegreerd met de Cosmos DB-service en het vereenvoudigen van de klantondersteuning.

Houd er rekening mee dat u afhankelijk bent van deze headers dat u de draagbaarheid van uw toepassing beperkt naar andere Gremlin-implementaties. In ruil daarvoor krijgt u een nauwere integratie met Cosmos DB Gremlin. Deze headers zijn geen TinkerPop standaard.

## <a name="headers"></a>Headers

| Header | Type | Voorbeeldwaarde | Wanneer inbegrepen | Uitleg |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | double | 11.3243 | Geslaagde en mislukte bewerkingen | Hoeveelheid verzameling of databasedoorvoer die wordt verbruikt in [aanvraageenheden (RU/s of RU's)](request-units.md) voor een gedeeltelijk antwoordbericht. Deze koptekst is aanwezig in elke voortzetting voor aanvragen met meerdere segmenten. Het weerspiegelt de kosten van een bepaalde reactie brok. Alleen voor aanvragen die bestaan uit één antwoordstuk komt deze koptekst overeen met de totale kosten van traversal. Voor de meeste complexe traversals vertegenwoordigt deze waarde echter een gedeeltelijke kostprijs. |
| **x-ms-total-request-charge** | double | 423.987 | Geslaagde en mislukte bewerkingen | Hoeveelheid verzameling of databasedoorvoer verbruikt in [aanvraageenheden (RU/s of RU's)](request-units.md) voor volledige aanvraag. Deze koptekst is aanwezig in elke voortzetting voor aanvragen met meerdere segmenten. Het geeft cumulatieve kosten aan sinds het begin van de aanvraag. De waarde van deze koptekst in de laatste chunk geeft volledige aanvraagkosten aan. |
| **x-ms-server-time-ms** | double | 13.75 | Geslaagde en mislukte bewerkingen | Deze header is opgenomen voor problemen met latentieproblemen. Het geeft de hoeveelheid tijd aan, in milliseconden, die Cosmos DB Gremlin-server heeft uitgevoerd en een gedeeltelijk antwoordbericht heeft geproduceerd. Door de waarde van deze header te gebruiken en te vergelijken met algemene aanvraaglatentietoepassingen kan de overhead van netwerklatentie worden berekend. |
| **x-ms-total-server-time-ms** | double | 130.512 | Geslaagde en mislukte bewerkingen | Totale tijd, in milliseconden, dat Cosmos DB Gremlin server nam om hele traversal uit te voeren. Deze header is opgenomen in elke gedeeltelijke reactie. Het vertegenwoordigt cumulatieve uitvoeringstijd sinds het begin van de aanvraag. Het laatste antwoord geeft de totale uitvoeringstijd aan. Deze header is handig om onderscheid te maken tussen client en server als een bron van latentie. U de uitvoeringstijd van de client vergelijken met de waarde van deze header. |
| **x-ms-status-code** | long | 200 | Geslaagde en mislukte bewerkingen | Header geeft interne reden aan voor het voltooien of beëindigen van het verzoek. Toepassing wordt geadviseerd om te kijken naar de waarde van deze header en corrigerende maatregelen te nemen. |
| **x-ms-substatus-code** | long | 1003 | Alleen mislukken | Cosmos DB is een database met meerdere modellen die is gebouwd bovenop de uniforme opslaglaag. Deze koptekst bevat aanvullende inzichten over de foutreden wanneer er een storing optreedt binnen lagere lagen van de stack met hoge beschikbaarheid. Toepassing wordt geadviseerd om deze header op te slaan en te gebruiken bij het contact opnemen met Cosmos DB customer support. Waarde van deze header is handig voor Cosmos DB engineer voor snelle probleemoplossing. |
| **x-ms-retry-after-ms** | tekenreeks (TimeSpan) | "00:00:03.9500000" | Alleen mislukken | Deze koptekst is een tekenreeksweergave van een .NET [TimeSpan-type.](https://docs.microsoft.com/dotnet/api/system.timespan) Deze waarde wordt alleen opgenomen in aanvragen die niet zijn uitgevoerd vanwege de ingerichte doorvoeruitputting. Aanvraag moet opnieuw traversal opnieuw indienen na geïnstrueerde periode van tijd. |
| **x-ms-activity-id** | tekenreeks (Guid) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Geslaagde en mislukte bewerkingen | Header bevat een unieke server-side id van een aanvraag. Aan elk verzoek wordt een unieke id van de server toegewezen voor trackingdoeleinden. Toepassingen moeten activiteits-id's registreren die door de server worden geretourneerd voor aanvragen waarover klanten mogelijk contact willen opnemen met de klantenservice. Cosmos DB-ondersteuningspersoneel kan specifieke aanvragen van deze id's vinden in Cosmos DB-servicetelemetrie. |

## <a name="status-codes"></a>Statuscodes

De meest voorkomende statuscodes die door de server worden geretourneerd, worden hieronder weergegeven.

| Status | Uitleg |
| --- | --- |
| **401** | Foutbericht `"Unauthorized: Invalid credentials provided"` wordt geretourneerd wanneer het verificatiewachtwoord niet overeenkomt met de cosmos DB-accountsleutel. Navigeer naar uw Cosmos DB Gremlin-account in de Azure-portal en controleer of de sleutel correct is.|
| **404** | Gelijktijdige bewerkingen die proberen dezelfde rand of hoekpunt tegelijkertijd te verwijderen en bij te werken. Foutbericht `"Owner resource does not exist"` geeft aan dat de opgegeven database of verzameling in de verbindingsparameters in de `/dbs/<database name>/colls/<collection or graph name>`-indeling onjuist is.|
| **408** | `"Server timeout"`geeft aan dat traversal meer dan **30 seconden** duurde en werd geannuleerd door de server. Optimaliseer uw traversals om snel te worden uitgevoerd door vertices of randen op elke hop van traversal te filteren om het zoekbereik te beperken.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` Dit gebeurt meestal wanneer een hoekpunt of rand met een id al in de graaf bestaat.| 
| **412** | Statuscode wordt aangevuld `"PreconditionFailedException": One of the specified pre-condition is not met`met een foutbericht . Deze fout is indicatief voor een optimistische gelijktijdigheid controle schending tussen het lezen van een rand of hoekpunt en het schrijven van het terug naar de winkel na wijziging. De meest voorkomende situaties wanneer deze fout `g.V('identifier').property('name','value')`optreedt, zijn bijvoorbeeld het wijzigen van eigendommen. Gremlin motor zou lezen het hoekpunt, wijzigen, en schrijf het terug. Als er een andere traversal loopt in parallel proberen om dezelfde hoekpunt of een rand te schrijven, een van hen zal deze fout te ontvangen. Toepassing moet traversal opnieuw indienen bij de server.| 
| **429** | Verzoek werd beperkt en moet opnieuw worden geprobeerd na waarde in **x-ms-retry-after-ms**| 
| **500** | Een foutbericht dat `"NotFoundException: Entity with the specified id does not exist in the system."` bevat, geeft aan dat een database en/of collectie opnieuw is gemaakt met dezelfde naam. De fout verdwijnt binnen vijf minuten, aangezien wijzigingen caches in verschillende Cosmos DB-onderdelen niet doorgeven en ongeldig maken. Als u dit probleem wilt voorkomen, gebruikt u telkens een unieke database en collectie.| 
| **1000** | Deze statuscode wordt geretourneerd wanneer de server een bericht ontleedt, maar niet kon worden uitgevoerd. Het duidt meestal op een probleem met de query.| 
| **1001** | Deze code wordt geretourneerd wanneer de server de traversal-uitvoering voltooit, maar verzuimt de respons terug te sturen naar de client. Deze fout kan optreden wanneer traversal complex resultaat genereert, dat te groot is of niet voldoet aan de TinkerPop-protocolspecificatie. Toepassing moet vereenvoudigen de traversal wanneer het ondervindt deze fout. | 
| **1003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"`wordt geretourneerd wanneer traversal de toegestane geheugenlimiet overschrijdt. Geheugenlimiet is **2 GB** per traversal.| 
| **1004** | Deze statuscode geeft een verkeerd gevormde grafiekaanvraag aan. Aanvraag kan misvormd zijn wanneer het niet deserialisatie, niet-waarde type wordt gedeserialiseerd als waardetype of niet-ondersteunde gremlin operatie gevraagd. Toepassing mag het verzoek niet opnieuw proberen omdat het niet succesvol zal zijn. | 
| **1007** | Meestal wordt deze statuscode `"Could not process request. Underlying connection has been closed."`geretourneerd met een foutbericht . Deze situatie kan optreden als clientstuurprogramma's een verbinding proberen te gebruiken die door de server wordt gesloten. Toepassing moet opnieuw proberen de traversal op een andere verbinding.
| **1008** | Cosmos DB Gremlin-server kan verbindingen beëindigen om verkeer in het cluster opnieuw in evenwicht te brengen. Clientstuurprogramma's moeten deze situatie afhandelen en alleen live verbindingen gebruiken om aanvragen naar de server te verzenden. Af en toe kunnen clientstuurprogramma's niet detecteren dat de verbinding is gesloten. Wanneer de toepassing `"Connection is too busy. Please retry after sometime or open more connections."` een fout tegenkomt, moet het traversal opnieuw proberen op een andere verbinding.

## <a name="samples"></a>Voorbeelden

Een voorbeeldclienttoepassing op basis van Gremlin.Net die één statuskenmerk leest:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Een voorbeeld dat laat zien hoe je statuskenmerk van gremlin java-client lezen:

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>Volgende stappen
* [HTTP-statuscodes voor Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Algemene Azure Cosmos DB REST-antwoordkoppen](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [TinkerPop Graph Driver Provider Eisen]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)

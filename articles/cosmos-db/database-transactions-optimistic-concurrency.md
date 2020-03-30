---
title: Databasetransacties en optimistische gelijktijdigheidscontrole in Azure Cosmos DB
description: In dit artikel worden databasetransacties en optimistische gelijktijdigheidscontrole in Azure Cosmos DB beschreven
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/04/2019
ms.reviewer: sngun
ms.openlocfilehash: d453bb4071c4a6972e01b8f7e90375181caf6d01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806521"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Transacties en optimistisch beheer van gelijktijdigheid

Databasetransacties bieden een veilig en voorspelbaar programmeermodel om gelijktijdige wijzigingen in de gegevens aan te kunnen. Met traditionele relationele databases, zoals SQL Server, u de bedrijfslogica schrijven met behulp van opgeslagen procedures en/of triggers, deze rechtstreeks naar de server verzenden voor uitvoering binnen de database-engine. Met traditionele relationele databases bent u verplicht om te gaan met twee verschillende programmeertalen de (niet-transactionele) programmeertaal van toepassingen zoals JavaScript, Python, C#, Java, enz.

De databaseengine in Azure Cosmos DB ondersteunt volledige ZUUR-(Atomicity,Consistency, Isolation, Sustainability) compatibele transacties met momentopnameisolatie. Alle databasebewerkingen binnen het bereik van de [logische partitie](partition-data.md) van een container worden transactioneel uitgevoerd binnen de database-engine die wordt gehost door de replica van de partitie. Deze bewerkingen omvatten zowel schrijven (het bijwerken van een of meer items binnen de logische partitie) als leesbewerkingen. In de volgende tabel worden verschillende bewerkingen en transactietypen geïllustreerd:

| **Bewerking**  | **Type bewerking** | **Transactie met één of meerdere objecten** |
|---------|---------|---------|
| Invoegen (zonder een pre/post trigger) | Schrijven | Transactie met één object |
| Invoegen (met een pre/post trigger) | Schrijven en lezen | Transactie met meerdere objecten |
| Vervangen (zonder een pre/post trigger) | Schrijven | Transactie met één object |
| Vervangen (met een pre/post trigger) | Schrijven en lezen | Transactie met meerdere objecten |
| Upsert (zonder een pre/post trigger) | Schrijven | Transactie met één object |
| Upsert (met een pre/post trigger) | Schrijven en lezen | Transactie met meerdere objecten |
| Verwijderen (zonder een pre/post trigger) | Schrijven | Transactie met één object |
| Verwijderen (met een pre/post trigger) | Schrijven en lezen | Transactie met meerdere objecten |
| Opgeslagen procedure uitvoeren | Schrijven en lezen | Transactie met meerdere objecten |
| Systeem gestart met uitvoering van een samenvoegprocedure | Schrijven | Transactie met meerdere objecten |
| Systeem gestarte uitvoering van het verwijderen van items op basis van vervaldatum (TTL) van een item | Schrijven | Transactie met meerdere objecten |
| Lezen | Lezen | Transactie met één object |
| Feed wijzigen | Lezen | Transactie met meerdere objecten |
| Paginated Lezen | Lezen | Transactie met meerdere objecten |
| Pagina-query | Lezen | Transactie met meerdere objecten |
| UDF uitvoeren als onderdeel van de pagina-query | Lezen | Transactie met meerdere objecten |

## <a name="multi-item-transactions"></a>Transacties met meerdere objecten

Met Azure Cosmos DB u [opgeslagen procedures, pre/post-triggers, door de gebruiker gedefinieerde functies (UDF's)](stored-procedures-triggers-udfs.md) en samenvoegprocedures in JavaScript schrijven. Azure Cosmos DB ondersteunt javascript-uitvoering in zijn database-engine. U opgeslagen procedures, pre/post-triggers, door de gebruiker gedefinieerde functies (UDF's) registreren en procedures voor het samenvoegen van een container registreren en deze later transactioneel uitvoeren binnen de Azure Cosmos-databaseengine. Als u toepassingslogica schrijft in JavaScript, u de controlestroom, variabele scoping, toewijzing en integratie van uitzonderingsverwerking primitieven binnen de databasetransacties rechtstreeks in de JavaScript-taal gebruiken.

De op JavaScript gebaseerde opgeslagen procedures, triggers, UDF's en samenvoegprocedures worden verpakt in een ambient ACID-transactie met momentopnameisolatie voor alle items binnen de logische partitie. In de loop van de uitvoering, als het JavaScript-programma een uitzondering werpt, wordt de hele transactie afgebroken en teruggedraaid. Het resulterende programmeermodel is eenvoudig maar krachtig. JavaScript-ontwikkelaars krijgen een duurzaam programmeermodel terwijl ze nog steeds hun vertrouwde taalconstructies en bibliotheekprimitieven gebruiken.

De mogelijkheid om JavaScript rechtstreeks in de database-engine uit te voeren biedt prestaties en transactionele uitvoering van databasebewerkingen ten opzichte van de items van een container. Aangezien azure cosmos-databaseengine json en JavaScript native ondersteunt, is er bovendien geen impedantie-mismatch tussen de typesystemen van een toepassing en de database.

## <a name="optimistic-concurrency-control"></a>Optimistische gelijktijdigheidscontrole

Optimistische gelijktijdigheidcontrole stelt u in staat om verloren updates en verwijderingen te voorkomen. Gelijktijdige, conflicterende bewerkingen worden onderworpen aan de regelmatige pessimistische vergrendeling van de database-engine die wordt gehost door de logische partitie die eigenaar is van het item. Wanneer twee gelijktijdige bewerkingen proberen om de nieuwste versie van een item bij te werken binnen een logische partitie, een van hen zal winnen en de andere zal mislukken. Als een of twee bewerkingen die hetzelfde item proberen gelijktijdig bij te werken, eerder een oudere waarde van het item hadden gelezen, weet de database echter niet of de eerder gelezen waarde door een of beide conflicterende bewerkingen inderdaad de laatste waarde van het item was. Gelukkig kan deze situatie worden gedetecteerd met de **Optimistische Concurrency Control (OCC)** voordat de twee bewerkingen de transactiegrens binnen de database-engine invoeren. OCC beschermt uw gegevens tegen het per ongeluk overschrijven van wijzigingen die door anderen zijn aangebracht. Het voorkomt ook dat anderen per ongeluk uw eigen wijzigingen overschrijven.

De gelijktijdige updates van een item worden onderworpen aan de OCC door de communicatieprotocollaag van Azure Cosmos DB. Azure Cosmos-database zorgt ervoor dat de clientversie van het item dat u bijwerkt (of verwijderd) hetzelfde is als de versie van het item in de Azure Cosmos-container. Dit garandeert dat uw schrijft worden beschermd tegen per ongeluk worden overschreven door de schrijft van anderen en vice versa. In een omgeving met meerdere gebruikers beschermt de optimistische gelijktijdigheidscontrole u tegen het per ongeluk verwijderen of bijwerken van de verkeerde versie van een item. Als zodanig, items zijn beschermd tegen de beruchte "verloren update" of "verloren verwijderen" problemen.

Elk item dat is opgeslagen in een `_etag` Azure Cosmos-container heeft een door het systeem gedefinieerde eigenschap. De waarde `_etag` van de wordt automatisch gegenereerd en bijgewerkt door de server elke keer dat het item wordt bijgewerkt. `_etag`kan worden gebruikt met `if-match` de meegeleverde aanvraagheader van de client, zodat de server kan beslissen of een item voorwaardelijk kan worden bijgewerkt. De waarde `if-match` van de koptekst `_etag` komt overeen met de waarde van de op de server, het item wordt vervolgens bijgewerkt. Als de waarde `if-match` van de aanvraagkopniet meer aanwezig is, weigert de server de bewerking met een antwoordbericht 'HTTP 412 Precondition failure'. De client kan vervolgens het item opnieuw ophalen om de huidige versie van het item op `_etag` de server te verkrijgen of de versie van het item in de server overschrijven met zijn eigen waarde voor het item. Bovendien `_etag` kan worden gebruikt `if-none-match` met de koptekst om te bepalen of een refetch van een resource nodig is.

De waarde `_etag` van het artikel verandert elke keer dat het item wordt bijgewerkt. Voor het vervangen `if-match` van artikelbewerkingen moet expliciet worden uitgedrukt als een deel van de aanvraagopties. Zie bijvoorbeeld de voorbeeldcode in [GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L578-L674). `_etag`waarden worden impliciet gecontroleerd op alle geschreven items die door de opgeslagen procedure worden aangeraakt. Als er een conflict wordt gedetecteerd, wordt de transactie door de opgeslagen procedure teruggedraaid en wordt er een uitzondering gemaakt. Met deze methode worden alle of geen schrijfbewerkingen binnen de opgeslagen procedure atomair toegepast. Dit is een signaal naar de toepassing om updates opnieuw toe te passen en het oorspronkelijke clientverzoek opnieuw te proberen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over databasetransacties en optimistische gelijktijdigheidscontrole in de volgende artikelen:

- [Werken met Azure Cosmos-databases, containers en items](databases-containers-items.md)
- [Consistentieniveaus](consistency-levels.md)
- [Conflicttypen en oplossingsbeleid](conflict-resolution-policies.md)
- [Opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies](stored-procedures-triggers-udfs.md)

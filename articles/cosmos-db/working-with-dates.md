---
title: Werken met datums in Azure Cosmos DB
description: Informatie over het opslaan, indexeren en opvragen van DataTime-objecten in Azure Cosmos DB
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 92fa35fbe8e5eef4dbdc8b6c47a9055affd449a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273191"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Werken met datums in Azure Cosmos DB

Azure Cosmos DB biedt schemaflexibiliteit en uitgebreide indexering via een native [JSON-gegevensmodel.](https://www.json.org) Alle Azure Cosmos DB-bronnen, waaronder databases, containers, documenten en opgeslagen procedures, worden gemodelleerd en opgeslagen als JSON-documenten. Json (en Azure Cosmos DB) ondersteunen als vereiste dat json (en Azure Cosmos DB) slechts een kleine set basistypen ondersteunen: Tekenreeks, Getal, Boolean, Array, Object en Null. Json is echter flexibel en stelt ontwikkelaars en frameworks in staat om complexere typen weer te geven met behulp van deze primitieven en ze samen te stellen als objecten of arrays.

Naast de basistypen hebben veel toepassingen het Type DateTime nodig om datums en tijdstempels weer te geven. In dit artikel wordt beschreven hoe ontwikkelaars datums kunnen opslaan, ophalen en query's in Azure Cosmos DB kunnen opslaan, ophalen en opvragen met behulp van de .NET SDK.

## <a name="storing-datetimes"></a>Datumtijd opslaan

Azure Cosmos DB ondersteunt JSON-typen zoals - tekenreeks, getal, booleaan, null, array, object. Het ondersteunt niet direct een DateTime-type. Azure Cosmos DB ondersteunt momenteel geen lokalisatie van datums. U moet DateTimes dus als tekenreeksen opslaan. De aanbevolen indeling voor DateTime-tekenreeksen `YYYY-MM-DDThh:mm:ss.sssZ` in Azure Cosmos DB is die de ISO 8601 UTC-standaard volgt. Het wordt aanbevolen om alle datums in Azure Cosmos DB op te slaan als UTC. Als u de datumtekenreeksen naar deze indeling converteert, kunnen de sorteerdatums lexicografisch worden gesorteerd. Als niet-UTC-datums worden opgeslagen, moet de logica aan de clientzijde worden verwerkt. Als u een lokale DateTime wilt converteren naar UTC, moet de verschuiving bekend/opgeslagen staan als een eigenschap in de JSON en kan de client de verschuiving gebruiken om de UTC DateTime-waarde te berekenen.

De meeste toepassingen kunnen de standaardtekenreeksweergave voor DateTime gebruiken om de volgende redenen:

* Tekenreeksen kunnen worden vergeleken en de relatieve volgorde van de DatumTime-waarden blijft behouden wanneer ze worden omgezet in tekenreeksen.
* Voor deze aanpak zijn geen aangepaste code of kenmerken vereist voor JSON-conversie.
* De data zoals opgeslagen in JSON zijn menselijk leesbaar.
* Deze aanpak kan profiteren van de azure cosmos DB-index voor snelle queryprestaties.

In het volgende fragment `Order` wordt bijvoorbeeld een object `ShipDate` opgeslagen `OrderDate` dat twee DateTime-eigenschappen bevat en als document met de .NET SDK:

```csharp
    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await container.CreateItemAsync(
        new Order
        {
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });
```

Dit document wordt als volgt opgeslagen in Azure Cosmos DB:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

U DateTimes ook opslaan als Unix-tijdstempels, dat wil zeggen als een getal dat het aantal verstreken seconden sinds 1 januari 1970 vertegenwoordigt. De interne eigenschap Timestamp`_ts`() van Azure Cosmos DB volgt deze aanpak. U de klasse [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) gebruiken om DateTimes als getallen te serialiseren.

## <a name="querying-datetimes-in-linq"></a>DateTimes opvragen in LINQ

De SQL .NET SDK ondersteunt automatisch querygegevens die via LINQ zijn opgeslagen in Azure Cosmos DB. In het volgende fragment wordt bijvoorbeeld een LINQ-query weergegeven waarin orders worden gefilterd die in de afgelopen drie dagen zijn verzonden:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Vertaald naar de volgende SQL-instructie en uitgevoerd op Azure Cosmos DB:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")
```

Meer informatie over de SQL-querytaal van Azure Cosmos DB en de LINQ-provider vindt u [in LINQ.](sql-query-linq-to-sql.md)

## <a name="indexing-datetimes-for-range-queries"></a>Datumtimes indexeren voor bereikquery's

Query's komen vaak voor met DatumTime-waarden. Als u deze query's efficiënt wilt uitvoeren, moet u een index hebben gedefinieerd op alle eigenschappen in het filter van de query.

Meer informatie over het configureren van indexeringsbeleid vindt u in [Azure Cosmos DB-indexeringsbeleid](index-policy.md). 

## <a name="next-steps"></a>Volgende stappen

* De codevoorbeelden downloaden en uitvoeren [op GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Meer informatie over [SQL-query's](sql-query-getting-started.md)
* Meer informatie over [Azure Cosmos DB-indexeringsbeleid](index-policy.md)

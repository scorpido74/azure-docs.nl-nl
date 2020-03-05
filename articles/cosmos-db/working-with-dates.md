---
title: Werken met datums in Azure Cosmos DB
description: Meer informatie over het opslaan, indexeren en doorzoeken van datum/tijd-objecten in Azure Cosmos DB
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 92fa35fbe8e5eef4dbdc8b6c47a9055affd449a5
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273191"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Werken met datums in Azure Cosmos DB

Azure Cosmos DB biedt flexibiliteit en uitgebreide indexeringen voor het schema via een systeem eigen [JSON](https://www.json.org) -gegevens model. Alle Azure Cosmos DB-resources, zoals databases, containers, documenten en opgeslagen procedures worden gemodelleerd en opgeslagen als JSON-documenten. Als een vereiste voor draagbare wordt JSON (en Azure Cosmos DB) ondersteunt een kleine set basistypen: tekenreeks, getal, Booleaanse waarde, matrix, Object en Null. Echter, JSON is flexibel en kunnen ontwikkelaars en frameworks voor complexere typen met behulp van deze primitieven en samenstellen van deze objecten of-matrices.

Naast de basis typen hebben veel toepassingen het type datum/tijd nodig om datums en tijds tempels weer te geven. Dit artikel wordt beschreven hoe ontwikkelaars kunnen opslaan, ophalen en query uitvoeren op datums in Azure Cosmos DB met behulp van de .NET SDK.

## <a name="storing-datetimes"></a>Opslaan van datum/tijd

Azure Cosmos DB ondersteunt JSON-typen zoals-String, Number, Boolean, null, array en object. Het biedt geen rechtstreekse ondersteuning voor het type datum/tijd. Op dit moment biedt Azure Cosmos DB geen ondersteuning voor lokalisatie van datums. U moet dus DateTimes opslaan als teken reeksen. De aanbevolen notatie voor datum-en tijd teken reeksen in Azure Cosmos DB is `YYYY-MM-DDThh:mm:ss.sssZ` die volgt op de norm ISO 8601 UTC. Het is raadzaam om alle datums in Azure Cosmos DB op te slaan als UTC. Als de datum teken reeksen naar deze indeling worden geconverteerd, kunnen de sorteer datums lexicographically worden gewijzigd. Als er niet-UTC-datums worden opgeslagen, moet de logica aan de client zijde worden afgehandeld. Voor het converteren van een lokale datum-/tijdwaarde naar UTC moet de offset bekend/opgeslagen zijn als een eigenschap in de JSON en de client kan de offset gebruiken om de waarde voor de UTC-datum/tijd te berekenen.

De meeste toepassingen kunnen de tekenreeksweergave van standaard gebruiken voor datum/tijd voor de volgende redenen:

* Tekenreeksen kunnen worden vergeleken, en de relatieve positie van de datum/tijd-waarden behouden blijft wanneer ze worden getransformeerd in tekenreeksen.
* Deze aanpak zijn vereist om een aangepaste code of kenmerken voor JSON-conversie.
* De datums die is opgeslagen in JSON zijn mens leesbaar is.
* Deze benadering kunt profiteren van Azure Cosmos DB-index voor de prestaties van snelle query's.

In het volgende code fragment wordt bijvoorbeeld een `Order`-object met twee DateTime-eigenschappen opgeslagen, `ShipDate` en `OrderDate` als een document met behulp van de .NET SDK:

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

Dit document is opgeslagen in Azure Cosmos DB als volgt:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

U kunt ook kunt u de datum/tijd als Unix tijdstempels, dat wil zeggen, opslaan als een getal dat het aantal verstreken seconden sinds 1 januari 1970 vertegenwoordigt. De eigenschap Internal Time Stamp (`_ts`) van Azure Cosmos DB volgt deze methode. U kunt de [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) -klasse gebruiken om datetimes als getallen te serialiseren.

## <a name="querying-datetimes-in-linq"></a>Een query uitvoeren op datum/tijd in LINQ

De SQL-SDK voor .NET ondersteunt automatisch het opvragen van gegevens die zijn opgeslagen in Azure Cosmos DB via LINQ. Het volgende code fragment toont bijvoorbeeld een LINQ-query waarmee orders worden gefilterd die in de afgelopen drie dagen zijn verzonden:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Vertaald naar de volgende SQL-instructie en uitgevoerd op Azure Cosmos DB:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")
```

Meer informatie over de SQL-query taal van Azure Cosmos DB en de LINQ-provider vindt u bij het [opvragen van Cosmos db in LINQ](sql-query-linq-to-sql.md).

## <a name="indexing-datetimes-for-range-queries"></a>Datum/tijd voor de bereik-query's indexeren

Query's zijn gebruikelijk met datum/tijd-waarden. Als u deze query's efficiënt wilt uitvoeren, moet u een index hebben gedefinieerd voor alle eigenschappen in het filter van de query.

Meer informatie over het configureren van het indexerings beleid in [Azure Cosmos DB indexerings beleid](index-policy.md). 

## <a name="next-steps"></a>Volgende stappen

* De code voorbeelden downloaden en uitvoeren [op github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Meer informatie over [SQL-query's](sql-query-getting-started.md)
* Meer informatie over [Azure Cosmos DB indexerings beleid](index-policy.md)

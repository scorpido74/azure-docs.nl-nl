---
title: Werken met datums in Azure Cosmos DB
description: Meer informatie over het werken met datums in Azure Cosmos DB.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: ea7880d051303afad01ad8ba4a2d68d7331c6a89
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71291148"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Werken met datums in Azure Cosmos DB
Azure Cosmos DB biedt schemaflexibiliteit en geavanceerde indexeermogelijkheden via een systeemeigen [JSON](https://www.json.org) gegevensmodel. Alle Azure Cosmos DB-resources, zoals databases, containers, documenten en opgeslagen procedures worden gemodelleerd en opgeslagen als JSON-documenten. Als vereiste voor een draag bare, JSON-(en Azure Cosmos DB) ondersteunt slechts een kleine set basis typen: Teken reeks, getal, Booleaanse waarde, matrix, object en null. Echter, JSON is flexibel en kunnen ontwikkelaars en frameworks voor complexere typen met behulp van deze primitieven en samenstellen van deze objecten of-matrices. 

Naast de basis typen hebben veel toepassingen het type datum/tijd nodig om datums en tijds tempels weer te geven. Dit artikel wordt beschreven hoe ontwikkelaars kunnen opslaan, ophalen en query uitvoeren op datums in Azure Cosmos DB met behulp van de .NET SDK.

## <a name="storing-datetimes"></a>Opslaan van datum/tijd

Azure Cosmos DB ligt binnen het bereik van de JSON-typen die geen DateTime-type bevatten. In Azure Cosmos DB moeten datums dus worden opgeslagen als teken reeksen. Op dit moment biedt Azure Cosmos DB geen ondersteuning voor lokalisatie van datums. De aanbevolen notatie voor datum-en tijd teken `YYYY-MM-DDThh:mm:ss.sssZ` reeksen in azure Cosmos DB is die de ISO 8601 UTC-standaard volgt. Als u de teken reeksen in deze indeling opmaakt, zijn de sorteer datums lexicographically. Logica voor het afhandelen van niet-UTC-datums moet door de client worden gedefinieerd. De meeste toepassingen kunnen de tekenreeksweergave van standaard gebruiken voor datum/tijd voor de volgende redenen:

* Tekenreeksen kunnen worden vergeleken, en de relatieve positie van de datum/tijd-waarden behouden blijft wanneer ze worden getransformeerd in tekenreeksen. 
* Deze aanpak zijn vereist om een aangepaste code of kenmerken voor JSON-conversie.
* De datums die is opgeslagen in JSON zijn mens leesbaar is.
* Deze benadering kunt profiteren van Azure Cosmos DB-index voor de prestaties van snelle query's.

Bijvoorbeeld, het volgende codefragment bevat een `Order` object met eigenschappen van twee datum/tijd - `ShipDate` en `OrderDate` als een document met de .NET SDK:

    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await client.CreateDocumentAsync("/dbs/orderdb/colls/orders", 
        new Order 
        { 
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });

Dit document is opgeslagen in Azure Cosmos DB als volgt:

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

U kunt ook kunt u de datum/tijd als Unix tijdstempels, dat wil zeggen, opslaan als een getal dat het aantal verstreken seconden sinds 1 januari 1970 vertegenwoordigt. Azure Cosmos DB van interne Timestamp (`_ts`) eigenschap deze aanpak volgt. U kunt de [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) klasse voor het serialiseren van datum/tijd as-nummers. 

## <a name="indexing-datetimes-for-range-queries"></a>Datum/tijd voor de bereik-query's indexeren
Bereik-query's zijn algemene met datum-/ tijdwaarden. Als u wilt zoeken naar alle bestellingen die zijn gemaakt sinds gisteren of zoeken naar alle bestellingen in de afgelopen vijf minuten hebt verzonden, moet u bijvoorbeeld bereik-query's uitvoeren. Voor het efficiënt uitvoeren van deze query's, moet u uw verzameling voor het bereik op tekenreeksen indexeren configureren.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

U kunt meer informatie over het configureren van de indexing beleid op [Azure Cosmos DB indexeren beleid](index-policy.md).

## <a name="querying-datetimes-in-linq"></a>Een query uitvoeren op datum/tijd in LINQ
De SQL-SDK voor .NET ondersteunt automatisch het opvragen van gegevens die zijn opgeslagen in Azure Cosmos DB via LINQ. Bijvoorbeeld, toont het volgende fragment een LINQ-query die filters orders die zijn verzonden in de afgelopen drie dagen.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on Azure Cosmos DB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

U kunt meer informatie over Azure Cosmos DB SQL-querytaal en de LINQ-provider op [uitvoeren van query's Cosmos DB](how-to-sql-query.md).

In dit artikel hebben bekeken hoe het opslaan, index- en het opvragen van datum/tijd in Azure Cosmos DB.

## <a name="next-steps"></a>Volgende stappen
* Downloaden en uitvoeren van de [codevoorbeelden op GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Meer informatie over [SQL-query's](how-to-sql-query.md)
* Meer informatie over [Azure Cosmos DB indexeren van beleid](index-policy.md)

---
title: Problemen met uitzonde ring Azure Cosmos DB niet gevonden oplossen
description: Een uitzonde ring vaststellen en oplossen die niet is gevonden
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f391772672904a1e2bdfa0a741d9f85a6edeea8b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294388"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found"></a>Problemen vaststellen en oplossen Azure Cosmos DB niet gevonden
De HTTP-status code 404 geeft aan dat de resource niet meer bestaat.

## <a name="expected-behavior"></a>Verwacht gedrag
Er zijn veel geldige scenario's waarbij een toepassing een 404 verwacht en het scenario op de juiste manier afhandelt.

## <a name="not-found-was-returned-for-an-item-that-should-exist-or-does-exist"></a>Er is niet gevonden voor een item dat moet bestaan of dat bestaat
Hieronder ziet u de mogelijke reden voor het retour neren van de status code 404 als het item wel of niet wordt afgesloten.

### <a name="1-race-condition"></a>1. race voorwaarde
Er zijn meerdere exemplaren van de SDK-client en het lezen is voorgekomen voordat de schrijf bewerking werd uitgevoerd.

#### <a name="solution"></a>Oplossing:
1. De standaard consistentie van het account voor Cosmos DB is sessie consistentie. Wanneer een item wordt gemaakt of bijgewerkt, wordt met het antwoord een sessie token geretourneerd dat kan worden door gegeven tussen SDK-instanties om te garanderen dat de Lees aanvraag wordt gelezen van een replica met die wijziging.
2. Het [consistentie niveau](consistency-levels-choosing.md) wijzigen in een [sterker niveau](consistency-levels-tradeoffs.md)

### <a name="2-invalid-partition-key-and-id-combination"></a>2. ongeldige combi natie van partitie sleutel en ID
De combi natie van de partitie sleutel en-ID is niet geldig.

#### <a name="solution"></a>Oplossing:
Herstel de toepassings logica die de onjuiste combi natie veroorzaakt. 

### <a name="3-invalid-character-in-item-id"></a>3. ongeldig teken in item-ID
Een item wordt ingevoegd in Cosmos DB met een [ongeldig teken](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks) in de item-id.

#### <a name="solution"></a>Oplossing:
Het wordt aanbevolen om de ID te wijzigen in een andere waarde die geen speciale tekens bevat. Als het wijzigen van de ID geen optie is, kunt u de ID met Base64 coderen om de speciale tekens te escapen.

Items die al in de container zijn ingevoegd de ID kan worden vervangen door RID-waarden in plaats van verwijzingen op basis van namen.
```c#
// Get a container reference that use RID values
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// List of invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // It recommend to chose a new ID that does not contain special characters, but
        // if that is not possible then it can be Base64 encoded to escape the special characters
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value using the rid based container reference
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validate the new ID can be read using the original name based contianer reference
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="4-time-to-live-ttl-purge"></a>4. time to Live (TTL) opschonen
De eigenschap [time to Live (TTL)](https://docs.microsoft.com/azure/cosmos-db/time-to-live) van het item is ingesteld. Het item is opgeschoond omdat de time-to-Live-periode is verlopen.

#### <a name="solution"></a>Oplossing:
Wijzig de time-to-Live om te voor komen dat het item wordt verwijderd.

### <a name="5-lazy-indexing"></a>5. luie indexering
De [luie indexering](index-policy.md#indexing-mode) is niet gevonden.

#### <a name="solution"></a>Oplossing:
Wacht totdat het indexeren is gestart of het indexerings beleid heeft gewijzigd

### <a name="6-parent-resource-deleted"></a>6. de bovenliggende resource is verwijderd
De data base en/of container waarin het item zich bevindt, zijn verwijderd.

#### <a name="solution"></a>Oplossing:
1. [Herstel](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period) de bovenliggende resource of maak de resources opnieuw.
2. Een nieuwe resource maken ter vervanging van de verwijderde resource

## <a name="next-steps"></a>Volgende stappen
* Problemen [vaststellen en oplossen](troubleshoot-dot-net-sdk.md) bij het gebruik van Azure Cosmos db .NET SDK
* Meer informatie over prestatie richtlijnen voor [.net v3](performance-tips-dotnet-sdk-v3-sql.md) en [.NET v2](performance-tips.md)
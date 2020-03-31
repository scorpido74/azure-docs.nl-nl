---
title: Opslagkosten optimaliseren in Azure Cosmos DB
description: In dit artikel wordt uitgelegd hoe u opslagkosten beheert voor de gegevens die zijn opgeslagen in Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 1508adda761fcba7ba70df3bb212d3eb4e32f242
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72754950"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Opslagkosten optimaliseren in Azure Cosmos DB

Azure Cosmos DB biedt onbeperkte opslag en doorvoer. In tegenstelling tot doorvoer, die u moet inrichten/configureren op uw Azure Cosmos-containers of databases, wordt de opslag gefactureerd op basis van verbruiksbasis. U wordt alleen gefactureerd voor de logische opslag die u verbruikt en u hoeft niet van tevoren opslag te reserveren. Opslag wordt automatisch op en neer schaalt op basis van de gegevens die u toevoegt of verwijdert aan een Azure Cosmos-container.

## <a name="storage-cost"></a>Opslagkosten

Opslag wordt gefactureerd met de eenheid van GB's. Lokale SSD-back-back-opslag wordt gebruikt door uw gegevens en indexering. De totale gebruikte opslag is gelijk aan de opslag die vereist is voor de gegevens en indexen die worden gebruikt in alle regio's waar u Azure Cosmos DB gebruikt. Als u wereldwijd een Azure Cosmos-account in drie regio's repliceert, betaalt u voor de totale opslagkosten in elk van deze drie regio's. Zie [het hulpprogramma voor capaciteitsplanner](https://www.documentdb.com/capacityplanner) om uw opslagbehoefte te schatten. De kosten voor opslag in Azure Cosmos DB zijn $ 0,25 GB/maand, zie [Prijspagina](https://azure.microsoft.com/pricing/details/cosmos-db/) voor de laatste updates. U waarschuwingen instellen om de opslag te bepalen die wordt gebruikt door uw Azure Cosmos-container, om uw opslag te controleren, zie [Monitor Azure Cosmos DB)](monitor-accounts.md)artikel.

## <a name="optimize-cost-with-item-size"></a>Kosten optimaliseren met objectgrootte

Azure Cosmos DB verwacht dat de grootte van het artikel 2 MB of minder is voor optimale prestatie- en kostenvoordelen. Als u een item nodig hebt om meer dan 2 MB aan gegevens op te slaan, u overwegen het artikelschema opnieuw op te slaan. In het zeldzame geval dat u het schema niet opnieuw ontwerpen, u het item splitsen in subitems en ze logisch koppelen aan een gemeenschappelijke id(ID). Alle Azure Cosmos DB-functies werken consistent door te verankeren aan die logische id.

## <a name="optimize-cost-with-indexing"></a>Kosten optimaliseren met indexering

Standaard worden de gegevens automatisch geïndexeerd, waardoor het totale verbruik van opslag kan toenemen. U echter aangepaste indexbeleidsregels toepassen om deze overhead te verminderen. Automatische indexering die niet is afgestemd door middel van beleid is ongeveer 10-20% van de grootte van het item. Door indexbeleid te verwijderen of aan te passen, betaalt u geen extra kosten voor schrijfbewerkingen en hebt u geen extra doorvoercapaciteit nodig. Zie [Indexering in Azure Cosmos DB](indexing-policies.md) om aangepaste indexeringsbeleidsregels te configureren. Als u al eerder met relationele databases hebt gewerkt, u denken dat alles indexeren een verdubbeling van de opslag of hoger betekent. In Azure Cosmos DB is het echter veel lager. In Azure Cosmos DB is de overhead van de index doorgaans laag (10-20%) zelfs met automatische indexering, omdat het is ontworpen voor een lage opslagvoetafdruk. Door het indexeringsbeleid te beheren, u de afweging van indexvoetafdruk en queryprestaties op een fijnmazige manier beheren.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Kosten optimaliseren met tijd om te leven en feed te wijzigen

Zodra u de gegevens niet meer nodig hebt, u deze op een elegante manier verwijderen uit uw Azure Cosmos-account door de tijd te gebruiken [om te leven,](time-to-live.md) [de feed te wijzigen](change-feed.md) of de oude gegevens te migreren naar een ander gegevensarchief, zoals Azure blob-opslag of Azure-gegevensmagazijn. Met de tijd om te leven of TTL, Azure Cosmos DB biedt de mogelijkheid om items automatisch te verwijderen uit een container na een bepaalde periode. Standaard u de tijd instellen om op containerniveau te leven en de waarde per item te overschrijven. Nadat u de TTL hebt ingesteld op een container of op itemniveau, verwijdert Azure Cosmos DB deze items automatisch na de periode sinds de tijddat ze voor het laatst zijn gewijzigd. Door wijzigingsfeed te gebruiken, u gegevens migreren naar een andere container in Azure Cosmos DB of naar een extern gegevensarchief. De migratie neemt nul down time en wanneer u klaar bent met migreren, u verwijderen of configureren tijd om te leven om de bron Azure Cosmos container te verwijderen.

## <a name="optimize-cost-with-rich-media-data-types"></a>Kosten optimaliseren met gegevenstypen voor rich media 

Als u rich media-typen wilt opslaan, bijvoorbeeld video's, afbeeldingen, enz., hebt u een aantal opties in Azure Cosmos DB. Een optie is om deze rich media types op te slaan als Azure Cosmos-items. Er is een limiet van 2 MB per item en u deze limiet vermijden door het gegevensitem in meerdere subitems te ketenen. U ze ook opslaan in Azure Blob-opslag en de metagegevens gebruiken om ernaar te verwijzen vanuit uw Azure Cosmos-items. Er zijn een aantal voor- en nadelen met deze aanpak. De eerste aanpak biedt u de beste prestaties op het gebied van latentie, doorvoerSLA's plus kant-en-klare wereldwijde distributiemogelijkheden voor de typen rich media-gegevens, naast uw reguliere Azure Cosmos-items. De ondersteuning is echter beschikbaar tegen een hogere prijs. Door media op te slaan in Azure Blob-opslag, u uw totale kosten verlagen. Als latentie van cruciaal belang is, u premiumopslag gebruiken voor rich media-bestanden waarnaar wordt verwezen vanuit Azure Cosmos-items. Dit integreert native met CDN om beelden van de edge-server te serveren tegen lagere kosten om geo-beperking te omzeilen. De keerzijde van dit scenario is dat u te maken hebt met twee services - Azure Cosmos DB en Azure Blob-opslag, waardoor de operationele kosten kunnen stijgen. 

## <a name="check-storage-consumed"></a>Opslag verbruikt controleren

Als u het opslagverbruik van een Azure Cosmos-container wilt controleren, `x-ms-request-quota` u `x-ms-request-usage` een HEAD- of GET-aanvraag op de container uitvoeren en de kop- en kopteksten inspecteren. Als u met de .NET SDK werkt, u ook de eigenschappen [DocumentSizeQuota](https://docs.microsoft.com/previous-versions/azure/dn850325(v%3Dazure.100))en [DocumentSizeUsage](https://msdn.microsoft.com/library/azure/dn850324.aspx) gebruiken om de opslag te laten verbruiken.

## <a name="using-sdk"></a>SDK gebruiken

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Volgende stappen

Vervolgens u meer te weten komen over kostenoptimalisatie in Azure Cosmos DB met de volgende artikelen:

* Meer informatie over [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over [het begrijpen van uw Azure Cosmos DB-factuur](understand-your-bill.md)
* Meer informatie over [het optimaliseren van doorvoerkosten](optimize-cost-throughput.md)
* Meer informatie over [het optimaliseren van de kosten van lezen en schrijven](optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten van query's](optimize-cost-queries.md)
* Meer informatie over [het optimaliseren van de kosten van Azure Cosmos-accounts met meerdere regio's](optimize-cost-regions.md)


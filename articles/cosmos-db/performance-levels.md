---
title: Gepensioneerde Azure Cosmos DB-prestatieniveaus
description: Meer informatie over de prestaties van S1, S2 en S3 die voorheen beschikbaar waren in Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.openlocfilehash: 40735f91e2ca58cc42f723c7993686d92f0e5ff0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623329"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Het buiten gebruik stellen van de prestatieniveaus S1, S2 en S3

> [!IMPORTANT] 
> De s1-, S2- en S3-prestatieniveaus die in dit artikel worden besproken, worden buiten gebruik gesteld en zijn niet langer beschikbaar voor nieuwe Azure Cosmos DB-accounts.
>

In dit artikel vindt u een overzicht van de prestatieniveaus van S1, S2 en S3 en wordt besproken hoe de verzamelingen die deze prestatieniveaus gebruiken, kunnen worden gemigreerd naar afzonderlijke partitieverzamelingen. Na het lezen van dit artikel u de volgende vragen beantwoorden:

- [Waarom worden de s1-, S2- en S3-prestatieniveaus met pensioen?](#why-retired)
- [Hoe verhouden afzonderlijke partitieverzamelingen en partitieverzamelingen zich tot de s1-, S2-, S3-prestatieniveaus?](#compare)
- [Wat moet ik doen om ononderbroken toegang tot mijn gegevens te garanderen?](#uninterrupted-access)
- [Hoe verandert mijn verzameling na de migratie?](#collection-change)
- [Hoe wordt mijn facturering gewijzigd nadat ik ben gemigreerd naar verzamelingen met één partitie?](#billing-change)
- [Wat moet ik doen als ik meer dan 20 GB opslagruimte nodig heb?](#more-storage-needed)
- [Kan ik wisselen tussen de prestatieniveaus S1, S2 en S3 vóór de geplande migratie?](#change-before)
- [Hoe migreer ik van de s1-, S2-, S3-prestatieniveaus naar afzonderlijke partitieverzamelingen op mijn eigen gebied?](#migrate-diy)
- [Hoe word ik beïnvloed als ik een EA-klant ben?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Waarom worden de prestaties van de S1, S2 en S3 met pensioen?

De prestaties van de S1-, S2- en S3-prestatieniveaus bieden niet de flexibiliteit die de standaard Azure Cosmos DB biedt. Met de S1, S2, S3 prestatieniveaus, zowel de doorvoer en opslagcapaciteit waren vooraf ingesteld en bood geen elasticiteit. Azure Cosmos DB biedt nu de mogelijkheid om uw doorvoer en opslag aan te passen, waardoor u veel meer flexibiliteit hebt in uw vermogen om te schalen naarmate uw behoeften veranderen.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Hoe verhouden afzonderlijke partitieverzamelingen en partitieverzamelingen zich tot de s1-, S2-, S3-prestatieniveaus?

In de volgende tabel worden de doorvoer- en opslagopties vergeleken die beschikbaar zijn in verzamelingen met één partitie, partitieverzamelingen en S1-, S2-, S3-prestatieniveaus. Hier is een voorbeeld voor de VS Oost 2 regio:

|   |Verdeelde verzameling|Verzameling met één partitie|S1|S2|S3|
|---|---|---|---|---|---|
|Maximale doorvoer|Onbeperkt|10.000 RU/s|250 RU/s|1 K RU/s|2,5 K RU/s|
|Minimale doorvoer|2,5 K RU/s|400 RU/s|250 RU/s|1 K RU/s|2,5 K RU/s|
|Maximale opslag|Onbeperkt|20 GB|20 GB|20 GB|20 GB|
|Prijs (maandelijks)|Doorvoer: $6 / 100 RU/s<br><br>Opslag: $ 0,25 / GB|Doorvoer: $6 / 100 RU/s<br><br>Opslag: $ 0,25 / GB|$25 USD|$ 50 USD|$ 100 USD|

Bent u klant bij EA? Zo ja, zie [hoe ben ik beïnvloed als ik een EA-klant ben?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Wat moet ik doen om ononderbroken toegang tot mijn gegevens te garanderen?

Als u een S1-, S2- of S3-verzameling hebt, moet u de verzameling programmatisch migreren naar één partitieverzameling [met behulp van de .NET SDK](#migrate-diy). 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Hoe verandert mijn verzameling na de migratie?

Als u een S1-verzameling hebt, u deze migreren naar één partitieverzameling met een doorvoersnelheid van 400 RU/s. 400 RU/s is de laagste doorvoer die beschikbaar is met één partitieverzameling. Echter, de kosten voor 400 RU / s in een partitie collectie is ongeveer hetzelfde als je betaalde met uw S1 collectie en 250 RU / s - dus je bent niet betalen voor de extra 150 RU / s voor u beschikbaar.

Als u een S2-verzameling hebt, u deze migreren naar één partitieverzameling met 1 K RU/s. U ziet geen wijziging in uw doorvoerniveau.

Als u een S3-verzameling hebt, u deze migreren naar één partitieverzameling met 2,5 K RU/s. U ziet geen wijziging in uw doorvoerniveau.

In elk van deze gevallen u, nadat u de verzameling migreert, uw doorvoerniveau aanpassen of zo nodig op en neer schalen om uw gebruikers toegang met een lage latentie te bieden. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Hoe wordt mijn facturering gewijzigd nadat ik ben gemigreerd naar de verzamelingen met één partitie?

Ervan uitgaande dat u 10 S1-verzamelingen hebt, 1 GB opslagruimte voor elk, in de regio VS-Oost, en u migreert deze 10 S1-verzamelingen naar 10 single partition-verzamelingen op 400 RU/sec (het minimumniveau). Uw factuur ziet er als volgt uit als u de 10 enkele partitieverzamelingen gedurende een volledige maand bewaart:

![Hoe S1-prijzen voor 10 collecties zich verhouden tot 10 verzamelingen met prijzen voor één partitieverzameling](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-20-gb-of-storage"></a>Wat moet ik doen als ik meer dan 20 GB opslagruimte nodig heb?

Of u nu een verzameling hebt met S1-, S2- of S3-prestatieniveau of één partitieverzameling hebt, die allemaal 20 GB opslagruimte beschikbaar hebben, u het hulpprogramma Azure Cosmos DB Data Migration gebruiken om uw gegevens te migreren naar een partitieverzameling met vrijwel onbeperkte opslag. Zie [Partitioneren en schalen in Azure Cosmos DB](sql-api-partition-data.md)voor informatie over de voordelen van een partitieverzameling. 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Kan ik wisselen tussen de prestatieniveaus S1, S2 en S3 vóór de geplande migratie?

Alleen bestaande accounts met S1-, S2- en S3-prestaties kunnen worden gewijzigd en de niveaus van het prestatieniveau programmatisch wijzigen [met behulp van de .NET SDK](#migrate-diy). Als u overstapt van S1, S3 of S3 in één partitieverzameling, u niet terugkeren naar de prestatieniveaus S1, S2 of S3.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Hoe migreer ik van de s1-, S2-, S3-prestatieniveaus naar afzonderlijke partitieverzamelingen op mijn eigen gebied?

U met de [.NET SDK](#migrate-diy)programmatisch migreren van de prestatieniveaus S1, S2 en S3 naar verzamelingen met één partitie. U dit voor de geplande migratie alleen doen om te profiteren van de flexibele doorvoeropties die beschikbaar zijn bij verzamelingen met één partitie.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Migreren naar verzamelingen met één partitie met behulp van de .NET SDK

Deze sectie heeft alleen betrekking op het wijzigen van het prestatieniveau van een verzameling met behulp van de [SQL .NET API,](sql-api-sdk-dotnet.md)maar het proces is vergelijkbaar voor onze andere SDK's.

Hier is een codefragment voor het wijzigen van de verzamelingsdoorvoer naar 5.000 aanvraageenheden per seconde:
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

Ga naar [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) voor het bekijken van aanvullende voorbeelden en meer informatie over onze aanbiedingsmethoden:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**OfferAsync vervangen**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**Voorstelvoorstel maken**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Hoe word ik beïnvloed als ik een EA-klant ben?

EA-klanten zijn beschermd tegen de prijs tot het einde van hun huidige contract.

## <a name="next-steps"></a>Volgende stappen
Ga voor meer informatie over prijzen en beheer van gegevens met Azure Cosmos DB naar de volgende bronnen:

1.  [Gegevens partitioneren in Cosmos DB](sql-api-partition-data.md). Begrijp het verschil tussen container container en partitiecontainers, evenals tips over het implementeren van een partitioneringsstrategie om naadloos te schalen.
2.  [Cosmos DB prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/). Meer informatie over de kosten van het inrichten van doorvoer en het verbruiken van opslag.
3.  [Eenheden aanvragen](request-units.md). Inzicht in het verbruik van doorvoer voor verschillende bewerkingstypen, bijvoorbeeld Lezen, Schrijven, Query.

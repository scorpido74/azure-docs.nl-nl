---
title: Azure Cosmos-containers maken met grote partitiesleutel
description: Meer informatie over het maken van een container in Azure Cosmos DB met grote partitiesleutel met Azure-portal en verschillende SDK's.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 7184a6b85e93c41dfe914813301a4b1a0c88f2cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887679"
---
# <a name="create-containers-with-large-partition-key"></a>Containers maken met grote partitiesleutel

Azure Cosmos DB maakt gebruik van hash-gebaseerde partitioneringregeling om horizontale schaling van gegevens te bereiken. Alle Azure Cosmos-containers die vóór 3 mei 2019 zijn gemaakt, gebruiken een hash-functie die hash berekent op basis van de eerste 100 bytes van de partitiesleutel. Als er meerdere partitiesleutels zijn met dezelfde eerste 100 bytes, worden deze logische partities door de service als dezelfde logische partitie beschouwd. Dit kan ertoe leiden dat problemen zoals het quotum voor partitiegrootte onjuist zijn en dat unieke indexen worden toegepast op de partitiesleutels. Grote partitiesleutels worden geïntroduceerd om dit probleem op te lossen. Azure Cosmos DB ondersteunt nu grote partitiesleutels met waarden tot 2 KB.

Grote partitiesleutels worden ondersteund door gebruik te maken van de functionaliteit van een verbeterde versie van de hash-functie, die een unieke hash kan genereren van grote partitiesleutels tot 2 KB. Deze hash-versie wordt ook aanbevolen voor scenario's met een hoge partitiesleuteldinaliteit, ongeacht de grootte van de partitiesleutel. Een partitiesleutelhoofdaldheid wordt gedefinieerd als het aantal unieke logische partities, bijvoorbeeld in de volgorde van ~ 30000 logische partities in een container. In dit artikel wordt beschreven hoe u een container maakt met een grote partitiesleutel met behulp van de Azure-portal en verschillende SDK's.

## <a name="create-a-large-partition-key-azure-portal"></a>Een grote partitiesleutel maken (Azure-portal)

Als u een grote partitiesleutel wilt maken, controleert u bij het maken van een nieuwe container met de Azure-portal de optie **Mijn partitiesleutel groter is dan 100 bytes.** Schakel het selectievakje uit als u geen grote partitiesleutels nodig hebt of als er toepassingen worden uitgevoerd op sdks-versie ouder dan 1.18.

![Grote partitiesleutels maken met Azure-portal](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Een grote partitiesleutel maken (PowerShell)

Als u een container wilt maken met ondersteuning voor grote partitiesleutelen, ziet u,

* [Een Azure Cosmos-container maken met een grote grootte van de partitiesleutel](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Een grote partitiesleutel maken (.Net SDK)

Als u een container met een grote partitiesleutel `PartitionKeyDefinitionVersion.V2` wilt maken met de .NET SDK, geeft u de eigenschap op. In het volgende voorbeeld ziet u hoe u de eigenschap Version opgeeft in het object PartitionKeyDefinition en deze instelt op PartitionKeyDefinitionVersion.V2.

### <a name="v3-net-sdk"></a>v3 .NET SDK

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

### <a name="v2-net-sdk"></a>v2 .NET SDK

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="supported-sdk-versions"></a>Ondersteunde SDK-versies

De grote partitiesleutels worden ondersteund met de volgende minimumversies van SDK's:

|SDK-type  | Minimale versie   |
|---------|---------|
|.Net     |    1.18     |
|Java-synchronisatie     |   2.4.0      |
|Java Async   |  2.5.0        |
| REST API | versie hoger `2017-05-03` dan `x-ms-version` met behulp van de aanvraagheader.|
| Resource Manager-sjabloon | versie 2 met `"version":2` behulp `partitionKey` van de eigenschap in het object. |

Momenteel u geen containers met een grote partitiesleutel gebruiken in Power BI en Azure Logic Apps. U containers gebruiken zonder een grote partitiesleutel van deze toepassingen.

## <a name="next-steps"></a>Volgende stappen

* [Partitionering in Azure Cosmos DB](partitioning-overview.md)
* [Aanvraageenheden in Azure Cosmos DB](request-units.md)
* [Doorvoer voor containers en databases inrichten](set-throughput.md)
* [Werken met een Azure Cosmos-account](account-overview.md)

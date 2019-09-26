---
title: Maak Azure Cosmos-containers met een grote partitie sleutel met behulp van de Azure Portal en diverse Sdk's.
description: Meer informatie over het maken van een container in Azure Cosmos DB met een grote partitie sleutel met behulp van Azure Portal en verschillende Sdk's.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: a1216daade2df832b606fceb648fca998c3fdec8
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300142"
---
# <a name="create-containers-with-large-partition-key"></a>Containers met een grote partitie sleutel maken

Azure Cosmos DB gebruikt een op hash gebaseerd partitie schema om gegevens Horizon taal te schalen. Alle Azure Cosmos-containers die zijn gemaakt vóór 3 2019 kunnen met een hash-functie worden berekend op basis van de eerste 100 bytes van de partitie sleutel. Als er meerdere partitie sleutels zijn met dezelfde eerste 100 bytes, worden deze logische partities beschouwd als dezelfde logische partitie door de service. Dit kan leiden tot problemen als het quotum voor de partitie grootte onjuist is en dat er unieke indexen worden toegepast op de partitie sleutels. Grote partitie sleutels zijn geïntroduceerd om dit probleem op te lossen. Azure Cosmos DB ondersteunt nu grote partitie sleutels met waarden van Maxi maal 2 KB.

Grote partitie sleutels worden ondersteund met behulp van de functionaliteit van een verbeterde versie van de hash-functie, waarmee een unieke hash kan worden gegenereerd van grote partitie sleutels tot 2 KB. Deze hash-versie wordt ook aanbevolen voor scenario's met een hoogwaardige kardinaliteit van de partitie, ongeacht de grootte van de partitie sleutel. Een partitie sleutel kardinaliteit wordt gedefinieerd als het aantal unieke logische partities, bijvoorbeeld in de volg orde van ~ 30000 logische partities in een container. In dit artikel wordt beschreven hoe u een container met een grote partitie sleutel maakt met behulp van de Azure Portal en verschillende Sdk's. 

## <a name="create-a-large-partition-key-net-sdk"></a>Een grote partitie sleutel maken (.NET SDK)

Als u een container met een grote partitie sleutel met behulp van de .net- `PartitionKeyDefinitionVersion.V2` SDK wilt maken, geeft u de eigenschap op. In het volgende voor beeld ziet u hoe u de eigenschap Version opgeeft in het PartitionKeyDefinition-object en deze instelt op PartitionKeyDefinitionVersion. v2.

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

## <a name="create-a-large-partition-key-azure-portal"></a>Een grote partitie sleutel maken (Azure Portal) 

Als u een grote partitie sleutel wilt maken en u een nieuwe container maakt met behulp van de Azure Portal, controleert u of de **sleutel mijn partitie groter is dan 100-bytes** . Standaard zijn alle nieuwe containers aangemeld met behulp van de grote partitie sleutels. Schakel het selectie vakje uit als u geen grote partitie sleutels nodig hebt of als u toepassingen hebt die worden uitgevoerd op de Sdk's-versie die ouder is dan 1,18.

![Grote partitie sleutels maken met behulp van Azure Portal](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Een grote partitie sleutel maken (Power shell)

Als u een container met een grote partitie sleutel wilt maken met behulp van `partitionKey` Power shell, neemt `"version" = 2` u voor het object op.

```azurepowershell-interactive
# Create a Cosmos SQL API container with large partition key support (version 2)
$resourceGroupName = "myResourceGroup"
$containerName = "mycosmosaccount" + "/sql/" + "myDatabase" + "/" + "myContainer"

# Container with large partition key support (version = 2)
$containerProperties = @{
  "resource"=@{
    "id"=$containerName;
    "partitionKey"=@{
        "paths"=@("/myPartitionKey");
        "kind"="Hash";
        "version" = 2
    };
    "indexingPolicy"=@{
        "indexingMode"="Consistent";
        "includedPaths"= @(@{
            "path"="/*"
        });
        "excludedPaths"= @(@{
            "path"="/myPathToNotIndex/*"
        })
    }
  }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerName -PropertyObject $containerProperties
```

## <a name="supported-sdk-versions"></a>Ondersteunde SDK-versies

De grote partitie sleutels worden ondersteund met de volgende minimum versies van Sdk's:

|Type SDK  | Minimale versie   |
|---------|---------|
|.Net     |    1,18     |
|Java-synchronisatie     |   2.4.0      |
|Java async   |  2.5.0        |
| REST-API | versie hoger dan `2017-05-03` met behulp `x-ms-version` van de aanvraag header.|

Op dit moment kunt u geen containers met een grote partitie sleutel gebruiken in Power BI en Azure Logic Apps. U kunt containers gebruiken zonder een grote partitie sleutel van deze toepassingen.

## <a name="next-steps"></a>Volgende stappen

* [Partitionering in Azure Cosmos DB](partitioning-overview.md)
* [Aanvraageenheden in Azure Cosmos DB](request-units.md)
* [Door Voer voor het inrichten van containers en data bases](set-throughput.md)
* [Werken met een Azure Cosmos-account](account-overview.md)



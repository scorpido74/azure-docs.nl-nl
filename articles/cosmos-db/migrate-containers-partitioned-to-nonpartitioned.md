---
title: Niet-gepartitioneerde Azure Cosmos-containers migreren naar partitiecontainers
description: Meer informatie over het migreren van alle bestaande niet-partitiecontainers naar partitiecontainers.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mjbrown
ms.openlocfilehash: 742ef62895f3ef64e8fa22ab21d2947bee57776b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623354"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Niet-partitiecontainers migreren naar partitiecontainers

Azure Cosmos DB ondersteunt het maken van containers zonder partitiesleutel. Momenteel u niet-gepartitioneerde containers maken met Azure CLI en Azure Cosmos DB SDKs (.Net, Java, NodeJs) met een versie die minder dan of gelijk is aan 2.x. U niet-gepartitioneerde containers maken met behulp van de Azure-portal. Dergelijke niet-gepartitioneerde containers zijn echter niet elastisch en hebben een vaste opslagcapaciteit van 20 GB en doorvoerlimiet van 10K RU/s.

De niet-gepartitioneerde containers zijn verouderd en u moet uw bestaande niet-partitiecontainers migreren naar partitiecontainers om opslag en doorvoer te schalen. Azure Cosmos DB biedt een door het systeem gedefinieerd mechanisme om uw niet-partitiecontainers te migreren naar partitiecontainers. In dit document wordt uitgelegd hoe alle bestaande niet-partitiecontainers automatisch worden gemigreerd naar partitiecontainers. U alleen gebruikmaken van de functie voor automatische migratie als u de V3-versie van SDK's in alle talen gebruikt.

> [!NOTE]
> Momenteel u Azure Cosmos DB MongoDB- en Gremlin-API-accounts niet migreren met behulp van de stappen die in dit document worden beschreven.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Container migreren met de door het systeem gedefinieerde partitiesleutel

Om de migratie te ondersteunen, biedt Azure Cosmos `/_partitionkey` DB een door het systeem gedefinieerde partitiesleutel met de naam op alle containers die geen partitiesleutel hebben. U de definitie van de partitiesleutel niet wijzigen nadat de containers zijn gemigreerd. De definitie van een container die is gemigreerd naar een partitiecontainer, is bijvoorbeeld als volgt:

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```

Nadat de container is gemigreerd, `_partitionKey` u documenten maken door de eigenschap samen met de andere eigenschappen van het document te vullen. De `_partitionKey` eigenschap vertegenwoordigt de partitiesleutel van uw documenten.

Het kiezen van de juiste partitiesleutel is belangrijk om de ingerichte doorvoer optimaal te gebruiken. Zie voor meer informatie hoe u een artikel [met partitiesleutel kiest.](partitioning-overview.md)

> [!NOTE]
> U alleen gebruikmaken van de door het systeem gedefinieerde partitiesleutel als u de nieuwste/V3-versie van SDK's in alle talen gebruikt.

In het volgende voorbeeld wordt een voorbeeldcode weergegeven om een document te maken met de door het systeem gedefinieerde partitiesleutel en dat document te lezen:

**Json-weergave van het document**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

Zie voor het volledige voorbeeld de GitHub-opslagplaats [.Net-voorbeelden.][1]
                      
## <a name="migrate-the-documents"></a>De documenten migreren

Hoewel de containerdefinitie is verbeterd met een eigenschap partitiesleutel, worden de documenten in de container niet automatisch gemigreerd. Dit betekent dat het `/_partitionKey` eigenschappad van de systeempartitiesleutel niet automatisch wordt toegevoegd aan de bestaande documenten. U moet de bestaande documenten opnieuw verdelen door de documenten te lezen die `_partitionKey` zijn gemaakt zonder partitiesleutel en ze opnieuw te schrijven met de eigenschap in de documenten.

## <a name="access-documents-that-dont-have-a-partition-key"></a>Toegang tot documenten die geen partitiesleutel hebben

Toepassingen hebben toegang tot de bestaande documenten die geen partitiesleutel hebben met behulp van de speciale systeemeigenschap 'PartitionKey.None', dit is de waarde van de niet-gemigreerde documenten. U deze eigenschap gebruiken in alle CRUD- en querybewerkingen. In het volgende voorbeeld wordt een voorbeeld weergegeven om één document uit de NonePartitionKey te lezen. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

Zie de GitHub-opslagplaats [voor .Net-voorbeelden][1] voor het volledige voorbeeld over het opnieuw verdelen van de documenten. 

## <a name="compatibility-with-sdks"></a>Compatibiliteit met SDK's

Oudere versie van Azure Cosmos DB SDK's zoals V2.x.x en V1.x.x ondersteunen de eigenschap met systeemgedefinieerde partitiesleutel niet. Dus, wanneer u de containerdefinitie van een oudere SDK leest, bevat deze geen definitie van partitiesleutel en deze containers gedragen zich precies zoals voorheen. Toepassingen die zijn gebouwd met de oudere versie van SDKs blijven werken met niet-gepartitioneerd als is zonder wijzigingen. 

Als een gemigreerde container wordt verbruikt door de nieuwste/V3-versie van SDK en u begint met het vullen van de door het systeem gedefinieerde partitiesleutel in de nieuwe documenten, u dergelijke documenten niet meer openen (lezen, bijwerken, verwijderen, query's) van de oudere SDK's.

## <a name="known-issues"></a>Bekende problemen

**Query's voor het aantal items dat is ingevoegd zonder partitiesleutel met V3 SDK kan een hoger doorvoerverbruik inhouden**

Als u een query van de V3 SDK opvraagt voor de items die worden ingevoegd `PartitionKey.None` met V2 SDK of de items `PartitionKey.None` die zijn ingevoegd met behulp van de V3 SDK met parameter, kan de telquery meer RU/s verbruiken als de parameter wordt geleverd in de FeedOptions. We raden u aan de `PartitionKey.None` parameter niet te leveren als er geen andere items met een partitiesleutel worden ingevoegd.

Als er nieuwe items worden ingevoegd met verschillende waarden voor de partitiesleutel, `FeedOptions` telt het opvragen voor een dergelijk item door de juiste sleutel in te geven, geen problemen. Na het invoegen van nieuwe documenten met partitiesleutel, als u alleen het aantal documenten moet opvragen zonder de waarde van de partitiesleutel, kan die query opnieuw hogere RU/s opleveren die vergelijkbaar is met de reguliere partitieverzamelingen.

## <a name="next-steps"></a>Volgende stappen

* [Partitionering in Azure Cosmos DB](partitioning-overview.md)
* [Aanvraageenheden in Azure Cosmos DB](request-units.md)
* [Doorvoer voor containers en databases inrichten](set-throughput.md)
* [Werken met een Azure Cosmos-account](account-overview.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration
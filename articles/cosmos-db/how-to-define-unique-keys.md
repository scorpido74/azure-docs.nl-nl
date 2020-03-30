---
title: Unieke sleutels definiëren voor een Azure Cosmos-container
description: Meer informatie over het definiëren van unieke sleutels voor een Azure Cosmos-container met Azure-portal, PowerShell, .Net, Java en diverse andere SDK's.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: thweiss
ms.openlocfilehash: fa62495a7b51c9a06a91102299378c15e811eae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872108"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Unieke sleutels definiëren voor een Azure Cosmos-container

In dit artikel worden de verschillende manieren gepresenteerd om [unieke sleutels](unique-keys.md) te definiëren bij het maken van een Azure Cosmos-container. Het is momenteel mogelijk om deze bewerking uit te voeren via de Azure-portal of via een van de SDK's.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. [Maak een nieuw Azure Cosmos-account](create-sql-api-dotnet.md#create-account) of selecteer een bestaand account.

1. Open het deelvenster **Gegevensverkenner** en selecteer de container waaraan u wilt werken.

1. Klik op **Nieuwe container**.

1. Klik **in** het dialoogvenster Container toevoegen op **+ Unieke sleutel toevoegen** om een uniek sleutelitem toe te voegen.

1. Voer het pad(en) in van de unieke sleutelbeperking

1. Voeg indien nodig meer unieke sleutelitems toe door op **+ Unieke sleutel toevoegen**

    ![Schermafbeelding van het invoeren van unieke sleutelbeperkingen op Azure-portal](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-powershell"></a>Powershell gebruiken

Als u een container met unieke toetsen wilt maken, maakt [u een Azure Cosmos-container met unieke sleutel en TTL](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="use-the-net-sdk-v2"></a>Gebruik de .NET SDK V2

Bij het maken van een nieuwe container `UniqueKeyPolicy` met de [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)kan een object worden gebruikt om unieke sleutelbeperkingen te definiëren.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    PartitionKey = new PartitionKeyDefinition { Paths = new Collection<string>(new List<string> { "/myPartitionKey" }) },
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "/emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

## <a name="use-the-net-sdk-v3"></a>Gebruik de .NET SDK V3

Bij het maken van een nieuwe container met behulp van de [.NET SDK v3,](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)gebruik je de vloeiende API van de SDK om unieke sleutels op een beknopte en leesbare manier te declareren.

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithUniqueKey()
        .Path("/firstName")
        .Path("/lastName")
        .Path("/emailAddress")
    .Attach()
    .WithUniqueKey()
        .Path("/address/zipCode")
    .Attach()
    .CreateIfNotExistsAsync();
```

## <a name="use-the-java-sdk"></a>Gebruik de Java SDK

Bij het maken van een nieuwe `UniqueKeyPolicy` container met de Java [SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)kan een object worden gebruikt om unieke belangrijke beperkingen te definiëren.

```java
// create a new DocumentCollection object
DocumentCollection container = new DocumentCollection();
container.setId("container");

// create array of strings and populate them with the unique key paths
Collection<String> uniqueKey1Paths = new ArrayList<String>();
uniqueKey1Paths.add("/firstName");
uniqueKey1Paths.add("/lastName");
uniqueKey1Paths.add("/emailAddress");
Collection<String> uniqueKey2Paths = new ArrayList<String>();
uniqueKey2Paths.add("/address/zipCode");

// create UniqueKey objects and set their paths
UniqueKey uniqueKey1 = new UniqueKey();
UniqueKey uniqueKey2 = new UniqueKey();
uniqueKey1.setPaths(uniqueKey1Paths);
uniqueKey2.setPaths(uniqueKey2Paths);

// create a new UniqueKeyPolicy object and set its unique keys
UniqueKeyPolicy uniqueKeyPolicy = new UniqueKeyPolicy();
Collection<UniqueKey> uniqueKeys = new ArrayList<UniqueKey>();
uniqueKeys.add(uniqueKey1);
uniqueKeys.add(uniqueKey2);
uniqueKeyPolicy.setUniqueKeys(uniqueKeys);

// set the unique key policy
container.setUniqueKeyPolicy(uniqueKeyPolicy);

// create the container
client.createCollection(String.format("/dbs/%s", "database"), container, null);
```

## <a name="use-the-nodejs-sdk"></a>Gebruik de Node.js SDK

Bij het maken van een nieuwe container `UniqueKeyPolicy` met de [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos)kan een object worden gebruikt om unieke belangrijke beperkingen te definiëren.

```javascript
client.database('database').containers.create({
    id: 'container',
    uniqueKeyPolicy: {
        uniqueKeys: [
            { paths: ['/firstName', '/lastName', '/emailAddress'] },
            { paths: ['/address/zipCode'] }
        ]
    }
});
```

## <a name="use-the-python-sdk"></a>De Python SDK gebruiken

Bij het maken van een nieuwe container met de [Python SDK](https://pypi.org/project/azure-cosmos/)kunnen unieke sleutelbeperkingen worden opgegeven als onderdeel van het woordenboek dat als parameter wordt doorgegeven.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            {'paths': ['/firstName', '/lastName', '/emailAddress']},
            {'paths': ['/address/zipCode']}
        ]
    }
})
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [partitioneren](partition-data.md)
- Ontdek [hoe indexering werkt](index-overview.md)

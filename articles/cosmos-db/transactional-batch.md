---
title: Transactionele batch bewerkingen in Azure Cosmos DB met behulp van de .NET SDK
description: Meer informatie over het gebruik van TransactionalBatch in de Azure Cosmos DB .NET SDK voor het uitvoeren van een groep punt bewerkingen die slagen of mislukken.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 1f541b947c04619892291e47002ea9b0dbb6d38d
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340556"
---
# <a name="transactional-batch-operations-in-azure-cosmos-db-using-the-net-sdk"></a>Transactionele batch bewerkingen in Azure Cosmos DB met behulp van de .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Transactionele batch beschrijft een groep punt bewerkingen die moeten slagen of mislukken samen met dezelfde partitie sleutel in een container. In de .NET SDK wordt de- `TranscationalBatch` klasse gebruikt voor het definiëren van deze batch bewerkingen. Als alle bewerkingen zijn geslaagd in de volg orde waarin ze worden beschreven in de transactionele batch-bewerking, wordt de trans actie doorgevoerd. Als een bewerking echter mislukt, wordt de hele trans actie teruggedraaid.

## <a name="whats-a-transaction-in-azure-cosmos-db"></a>Wat is een trans actie in Azure Cosmos DB

Een trans actie in een typische data base kan worden gedefinieerd als een reeks bewerkingen die worden uitgevoerd als één logische werk eenheid. Met elke trans actie kunnen de eigenschappen van de zuur (atomiciteit, consistentie, isolatie, duurzaamheid) worden gegarandeerd.

* **Atomiciteit** garandeert dat alle bewerkingen die worden uitgevoerd binnen een trans actie, worden behandeld als één eenheid en dat allemaal zijn doorgevoerd of niet.
* **Consistentie** zorgt ervoor dat de gegevens altijd een geldige status over trans acties hebben.
* Door **isolatie** wordt gegarandeerd dat twee trans acties met elkaar in strijd zijn: veel commerciële systemen bieden meerdere isolatie niveaus die kunnen worden gebruikt op basis van de behoeften van de toepassing.
* **Duurzaamheid** zorgt ervoor dat alle wijzigingen die in een Data Base zijn doorgevoerd, altijd aanwezig zijn.
Azure Cosmos DB ondersteunt [Full zuur compatibele trans acties met snap shot-isolatie](database-transactions-optimistic-concurrency.md) voor bewerkingen binnen dezelfde [logische-partitie sleutel](partitioning-overview.md).

## <a name="transactional-batch-operations-vs-stored-procedures"></a>Transactionele batch bewerkingen versus opgeslagen procedures

Azure Cosmos DB ondersteunt momenteel opgeslagen procedures, die ook het transactionele bereik voor bewerkingen bieden. Transactionele batch bewerkingen bieden echter de volgende voor delen:

* **Taal optie** : transactionele batch wordt ondersteund op de SDK en de taal waarmee u al werkt, terwijl opgeslagen procedures moeten worden geschreven in Java script.
* **Code versie beheer** : toepassings code voor versie beheer en onboarding op uw CI/cd-pijp lijn is veel belang rijker dan het organiseren van de update van een opgeslagen procedure en ervoor te zorgen dat de rollover op het juiste moment plaatsvindt. De wijzigingen worden ook eenvoudiger ongedaan gemaakt.
* **Prestaties** : de latentie van gelijkwaardige bewerkingen tot 30% verkleind in vergelijking met de uitvoering van de opgeslagen procedure.
* **Serialisatie van inhoud** : elke bewerking binnen een transactionele batch kan gebruikmaken van aangepaste opties voor serialisatie voor de payload.

## <a name="how-to-create-a-transactional-batch-operation"></a>Een transactionele batch bewerking maken

Wanneer u een transactionele batch bewerking maakt, begint u met een container exemplaar en roept u het `CreateTransactionalBatch` volgende aan:

```csharp
string partitionKey = "The Family";
ParentClass parent = new ParentClass(){ Id = "The Parent", PartitionKey = partitionKey, Name = "John", Age = 30 };
ChildClass child = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatch batch = container.CreateTransactionalBatch(new PartitionKey(parent.PartitionKey)) 
  .CreateItem<ParentClass>(parent)
  .CreateItem<ChildClass>(child);
```

Vervolgens moet u de volgende aanroepen `ExecuteAsync` :

```csharp
TransactionalBatchResponse batchResponse = await batch.ExecuteAsync();
```

Zodra het antwoord is ontvangen, moet u controleren of het al dan niet is gelukt en de resultaten ophalen:

```csharp
using (batchResponse)
{
  if (batchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = batchResponse.GetOperationResultAtIndex<ParentClass>(0);
    ParentClass parentClassResult = parentResult.Resource;
    TransactionalBatchOperationResult<ChildClass> childResult = batchResponse.GetOperationResultAtIndex<ChildClass>(1);
    ChildClass childClassResult = childResult.Resource;
  }
}
```

Als er een fout optreedt, heeft de mislukte bewerking de status code van de bijbehorende fout. Dat alle andere bewerkingen een 424-status code (mislukte afhankelijkheid) hebben. In het onderstaande voor beeld mislukt de bewerking omdat deze probeert een item te maken dat al bestaat (409 HTTP status code. conflict). Status codes maken het gemakkelijker om de oorzaak van transactie storingen te identificeren.

```csharp
// Parent's birthday!
parent.Age = 31;
// Naming two children with the same name, should abort the transaction
ChildClass anotherChild = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatchResponse failedBatchResponse = await container.CreateTransactionalBatch(new PartitionKey(partitionKey))
  .ReplaceItem<ParentClass>(parent.Id, parent)
  .CreateItem<ChildClass>(anotherChild)
  .ExecuteAsync();

using (failedBatchResponse)
{
  if (!failedBatchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = failedBatchResponse.GetOperationResultAtIndex<ParentClass>(0);
    // parentResult.StatusCode is 424
    TransactionalBatchOperationResult<ChildClass> childResult = failedBatchResponse.GetOperationResultAtIndex<ChildClass>(1);
    // childResult.StatusCode is 409
  }
}
```

## <a name="how-are-transactional-batch-operations-executed"></a>Hoe worden transactionele batch bewerkingen uitgevoerd?

Wanneer de `ExecuteAsync` methode wordt aangeroepen, worden alle bewerkingen in het `TransactionalBatch` object gegroepeerd, geserialiseerd in één nettolading en verzonden als één aanvraag aan de Azure Cosmos DB-service.

De service ontvangt de aanvraag en voert alle bewerkingen binnen een transactionele scope uit en retourneert een antwoord met hetzelfde serialisatie protocol. Dit antwoord is een geslaagde of mislukte reactie en bevat alle afzonderlijke respons op de bewerking.

De SDK stelt het antwoord op u in staat om het resultaat te controleren en eventueel elk van de interne bewerkings resultaten te extra heren.

## <a name="limitations"></a>Beperkingen

Er zijn momenteel twee bekende limieten:

* De limiet voor de grootte van Azure Cosmos DB-aanvragen geeft aan dat de `TransactionalBatch` Payload niet groter mag zijn dan 2 MB en dat de maximale uitvoerings tijd 5 seconden is.
* Er is een huidige limiet van 100 bewerkingen per `TransactionalBatch` om ervoor te zorgen dat de prestaties zoals verwacht en binnen de sla's zijn.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [wat u kunt doen met TransactionalBatch](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/TransactionalBatch)

* Bekijk onze voor [beelden](sql-api-dotnet-v3sdk-samples.md) voor meer manieren om onze Cosmos db .NET SDK te gebruiken

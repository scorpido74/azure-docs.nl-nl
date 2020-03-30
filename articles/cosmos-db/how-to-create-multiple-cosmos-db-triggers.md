---
title: Meerdere onafhankelijke Azure Functions-triggers maken voor Cosmos DB
description: Meer informatie over het configureren van meerdere onafhankelijke Azure Functions-triggers voor Cosmos DB om door gebeurtenissen gestuurde architecturen te maken.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 32b680acdee29bf97a0e132fee93d5fee3377245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604941"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>Meerdere Azure Functions-triggers maken voor Cosmos DB

In dit artikel wordt beschreven hoe u meerdere Azure Functions-triggers zo kunt configureren dat Cosmos DB parallel werkt en onafhankelijk reageert op wijzigingen.

![Serverless event-based Functions working with the Azure Functions trigger for Cosmos DB and sharing a leases container Serverless event-based Functions working with the Azure Functions trigger for Cosmos DB and sharing a leases container Serverless event-based Functions working with the Azure Functions trigger for Cosmos DB and sharing a leases container Serverless](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>Vereisten voor architectuur op basis van evenementen

Bij het bouwen van serverloze architecturen met [Azure-functies](../azure-functions/functions-overview.md)is het [raadzaam](../azure-functions/functions-best-practices.md#avoid-long-running-functions) om kleine functiesets te maken die samenwerken in plaats van grote langlopende functies.

Terwijl u op gebeurtenissen gebaseerde serverloze stromen bouwt met behulp van de [Trigger azure-functies voor Cosmos DB,](./change-feed-functions.md)loopt u het scenario tegen het scenario aan waarin u meerdere dingen wilt doen wanneer er een nieuwe gebeurtenis in een bepaalde [Azure Cosmos-container is.](./databases-containers-items.md#azure-cosmos-containers) Als acties die u wilt activeren, onafhankelijk van elkaar zijn, is de ideale oplossing om **één Azure-functietriggers voor Cosmos DB per actie** te maken die u wilt uitvoeren, en alle wijzigingen op dezelfde Azure Cosmos-container.

## <a name="optimizing-containers-for-multiple-triggers"></a>Containers optimaliseren voor meerdere Triggers

Gezien de *vereisten* van de Azure Functions trigger voor Cosmos DB, hebben we een tweede container nodig om de status op te slaan, ook wel de *leasecontainer*genoemd. Betekent dit dat u een aparte leasecontainer nodig hebt voor elke Azure-functie?

Hier heb je twee opties:

* Eén **leasecontainer per functie maken:** deze aanpak kan zich vertalen in extra kosten, tenzij u een [gedeelde doorvoerdatabase gebruikt.](./set-throughput.md#set-throughput-on-a-database) Houd er rekening mee dat de minimale doorvoer op containerniveau 400 [aanvraageenheden](./request-units.md)is en in het geval van de leasecontainer alleen wordt gebruikt om de voortgang te controleren en de status te handhaven.
* Eén **leasecontainer hebben en deze delen** voor al uw functies: deze tweede optie maakt beter gebruik van de ingerichte aanvraageenheden op de container, omdat het meerdere Azure-functies in staat stelt om dezelfde ingerichte doorvoer te delen en te gebruiken.

Het doel van dit artikel is om u te begeleiden om de tweede optie te bereiken.

## <a name="configuring-a-shared-leases-container"></a>Een container met gedeelde leasen configureren

Als u de container met gedeelde leasen wilt configureren, hoeft u `LeaseCollectionPrefix` alleen op uw triggers `leaseCollectionPrefix` een extra configuratie te maken door het [kenmerk](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#attributes-and-annotations) toe te voegen als u C# of [kenmerk](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) gebruikt als u JavaScript gebruikt. De waarde van het kenmerk moet een logische beschrijving zijn van wat die specifieke trigger.

Als u bijvoorbeeld drie Triggers hebt: een die e-mails verzendt, een die een aggregatie doet om een gematerialiseerde `LeaseCollectionPrefix` weergave te maken, en een die de wijzigingen naar een andere opslag verzendt, voor latere analyse, u de van e-mails toewijzen aan de eerste, gematerialiseerd aan de tweede en analytics aan de derde.

Het belangrijkste is dat alle drie de Triggers **dezelfde leases containerconfiguratie** (account, database en containernaam) kunnen gebruiken.

Een zeer eenvoudige code `LeaseCollectionPrefix` monsters met behulp van het attribuut in C #, zou er als volgt uitzien:

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

[FunctionName("SendEmails")]
public static void SendEmails([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "emails")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}

[FunctionName("MaterializedViews")]
public static void MaterializedViews([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "materialized")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}
```

En voor JavaScript u de `function.json` configuratie op `leaseCollectionPrefix` het bestand toepassen met het kenmerk:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "emails"
},
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "materialized"
}
```

> [!NOTE]
> Controleer altijd op de aanvraageenheden die zijn ingericht op uw gedeelde leasecontainer. Elke trigger die deze deelt, verhoogt het gemiddelde doorvoergemiddelde verbruik, dus het kan nodig zijn om de ingerichte doorvoer te verhogen terwijl u het aantal Azure-functies dat het gebruikt verhoogt.

## <a name="next-steps"></a>Volgende stappen

* Bekijk de volledige configuratie voor de trigger van [Azure Functions voor Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)
* Controleer de uitgebreide [lijst met voorbeelden](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md) voor alle talen.
* Ga naar de Serverless-recepten met Azure Cosmos DB en Azure Functions [GitHub-repository](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) voor meer voorbeelden.
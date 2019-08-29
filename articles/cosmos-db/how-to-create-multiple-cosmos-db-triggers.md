---
title: Meerdere onafhankelijke Azure Functions triggers maken voor Cosmos DB
description: Meer informatie over het configureren van meerdere onafhankelijke Azure Functions triggers voor Cosmos DB voor het maken van op gebeurtenissen gebaseerde architecturen.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 987136bf8aba1313e1bef21f58691bf9a860ea32
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093381"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>Meerdere Azure Functions triggers maken voor Cosmos DB

In dit artikel wordt beschreven hoe u meerdere Azure Functions triggers zo kunt configureren dat Cosmos DB parallel werken en onafhankelijk reageren op wijzigingen.

![Functies die zijn gebaseerd op gebeurtenissen op basis van een server werken met de Azure Functions trigger voor het Cosmos DB en delen van een lease-container](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>Architectuur vereisten op basis van gebeurtenissen

Bij het bouwen van serverloze architecturen met [Azure functions](../azure-functions/functions-overview.md), is het [raadzaam](../azure-functions/functions-best-practices.md#avoid-long-running-functions) om kleine functie sets te maken die samen werken in plaats van grote langlopende functies.

Wanneer u op gebeurtenissen gebaseerde serverloze stromen bouwt met behulp [van de Azure functions trigger voor Cosmos DB](./change-feed-functions.md), voert u in het scenario uit waar u meerdere dingen wilt doen wanneer er een nieuwe gebeurtenis is in een bepaalde [Azure Cosmos-container](./databases-containers-items.md#azure-cosmos-containers). Als de acties die u wilt activeren, onafhankelijk van elkaar zijn, is de ideale oplossing om **een Azure functions triggers te maken voor Cosmos DB per actie** die u wilt uitvoeren. alle luistert naar wijzigingen in dezelfde Azure Cosmos-container.

## <a name="optimizing-containers-for-multiple-triggers"></a>Containers voor meerdere triggers optimaliseren

Gezien de *vereisten* van de Azure functions trigger voor Cosmos DB hebben we een tweede container nodig om de status op te slaan, ook wel de *container leases*. Betekent dit dat u een afzonderlijke leases-container nodig hebt voor elke Azure-functie?

Hier hebt u twee opties:

* **Eén leases-container maken per functie**: Deze methode kan worden omgezet in aanvullende kosten, tenzij u een [gedeelde doorvoer database](./set-throughput.md#set-throughput-on-a-database)gebruikt. Houd er rekening mee dat de minimale door Voer op het container niveau 400 [aanvraag eenheden](./request-units.md)is, en in het geval van de container leases, wordt dit alleen gebruikt om de voortgang te controleren en de status te onderhouden.
* Hebben **één lease container en delen deze** voor al uw functies: Deze tweede optie maakt beter gebruik van de ingerichte aanvraag eenheden in de container, omdat het meerdere Azure Functions mogelijk maakt om dezelfde ingerichte door voer te delen en te gebruiken.

Het doel van dit artikel is om u te helpen bij het uitvoeren van de tweede optie.

## <a name="configuring-a-shared-leases-container"></a>Een gedeelde lease-container configureren

Als u de container gedeelde leases wilt configureren, is de enige extra configuratie die u nodig hebt om uw triggers te `LeaseCollectionPrefix` maken, het [kenmerk](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---c-attributes) toe C# te `leaseCollectionPrefix` voegen als u of- [kenmerk](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---javascript-example) gebruikt als u Java script. De waarde van het kenmerk moet een logische descriptor zijn van wat de betreffende trigger is.

Als u bijvoorbeeld drie triggers hebt: een die e-mail berichten verzendt, een die een aggregatie maakt om een gerealiseerde weer gave te maken en een die de wijzigingen naar een andere opslag verzendt, kunt u voor latere analyse `LeaseCollectionPrefix` het e-mail berichten toewijzen aan de eerste. gerealiseerd naar de tweede en ' analyse ' tot de derde.

Het belang rijk deel is dat alle drie **de triggers dezelfde container configuratie** voor leases (account, data base en container naam) kunnen gebruiken.

Een eenvoudige code voorbeelden die gebruikmaken van `LeaseCollectionPrefix` het kenmerk C#in, ziet er als volgt uit:

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

En voor Java script kunt u de configuratie Toep assen op `function.json` het bestand, met `leaseCollectionPrefix` het kenmerk:

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
> Altijd controleren op de aanvraag eenheden die zijn ingericht in uw gedeelde leases-container. Elke trigger waarmee deze wordt gedeeld, verhoogt het verbruik van gemiddelde door Voer, dus u moet mogelijk de ingerichte door Voer verhogen als u het aantal Azure Functions dat het gebruikt, verhoogt.

## <a name="next-steps"></a>Volgende stappen

* Bekijk de volledige configuratie voor de [Azure functions trigger voor Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)
* Controleer de uitgebreide [lijst met voor beelden](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---example) voor alle talen.
* Ga naar de Serverloze recepten met Azure Cosmos DB en Azure Functions [github-opslag plaats](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios) voor meer voor beelden.
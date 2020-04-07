---
title: Migreren van de bulkexecutorbibliotheek naar de bulkondersteuning in Azure Cosmos DB .NET V3 SDK
description: Meer informatie over het migreren van uw toepassing van het gebruik van de bulkexecutorbibliotheek naar de bulkondersteuning in Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: maquaran
ms.openlocfilehash: 820a5398d84122659b1676b7d5722bce08b1837d
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755969"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Migreren van de bulkexecutorbibliotheek naar de bulkondersteuning in Azure Cosmos DB .NET V3 SDK

In dit artikel worden de vereiste stappen beschreven om de code van een bestaande toepassing te migreren die de [.NET-bulkexecutorbibliotheek](bulk-executor-dot-net.md) gebruikt naar de [bulkondersteuningsfunctie](tutorial-sql-api-dotnet-bulk-import.md) in de nieuwste versie van de .NET SDK.

## <a name="enable-bulk-support"></a>Bulkondersteuning inschakelen

Bulkondersteuning voor `CosmosClient` de instantie inschakelen via de [configuratie AllowBulkExecution:](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution)

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>Taken maken voor elke bewerking

Bulkondersteuning in de .NET SDK werkt door gebruik te maken van de [taakparallelbibliotheek](https://docs.microsoft.com/dotnet/standard/parallel-programming/task-parallel-library-tpl) en groepsbewerkingen die gelijktijdig plaatsvinden. 

Er is geen enkele methode die uw lijst met documenten of bewerkingen als invoerparameter neemt, maar u moet een taak maken voor elke bewerking die u in bulk wilt uitvoeren.

Als uw eerste invoer bijvoorbeeld een lijst is met items waarbij elk item het volgende schema heeft:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

Als u bulkimport wilt uitvoeren (vergelijkbaar met het gebruik van BulkExecutor.BulkImportAsync), moet u gelijktijdige aanroepen naar `CreateItemAsync` met elke artikelwaarde hebben. Bijvoorbeeld:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

Als u *bulkupdate* wilt uitvoeren (vergelijkbaar met het gebruik van [BulkExecutor.BulkUpdateAsync),](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)moet u na het bijwerken van de objectwaarde gelijktijdige aanroepen naar `ReplaceItemAsync` methode hebben. Bijvoorbeeld:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

En als u *bulkverwijderen* wilt doen (vergelijkbaar met het gebruik van [BulkExecutor.BulkDeleteAsync),](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)moet u gelijktijdige aanroepen naar `DeleteItemAsync`, met de `id` en partitiesleutel van elk item hebben. Bijvoorbeeld:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>Status taakresultaat vastleggen

In de vorige codevoorbeelden hebt u een gelijktijdige lijst `CaptureOperationResponse` met taken gemaakt en de methode voor elk van deze taken aangeroepen. Deze methode is een extensie waarmee we een *vergelijkbaar antwoordschema* kunnen behouden als BulkExecutor, door fouten vast te leggen en het [gebruik van de aanvraageenheden](request-units.md)bij te houden.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

Wanneer `OperationResponse` de wordt aangegeven als:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>Bewerkingen gelijktijdig uitvoeren

Nadat de lijst met taken is gedefinieerd, wacht u tot ze allemaal zijn voltooid. U de voltooiing van de taken bijhouden door het bereik van uw bulkbewerking te definiëren zoals weergegeven in het volgende codefragment:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>Statistieken vastleggen

De vorige code wacht tot alle bewerkingen zijn voltooid en berekent de vereiste statistieken. Deze statistieken zijn vergelijkbaar met die van bulk executor library [BulkImportResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse).

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

De `BulkOperationResponse` bevat:

1. De totale tijd die nodig is om de lijst met bewerkingen te verwerken via bulkondersteuning.
1. Het aantal succesvolle operaties.
1. Het totaal van de gevraagde eenheden verbruikt.
1. Als er fouten zijn, wordt een lijst met tuples weergegeven die de uitzondering en het bijbehorende item voor logboekregistratie en identificatiedoel bevatten.

## <a name="retry-configuration"></a>Configuratie opnieuw proberen

Bulk executor bibliotheek had [begeleiding](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account) `MaxRetryWaitTimeInSeconds` die `MaxRetryAttemptsOnThrottledRequests` vermeld om de en van [RetryOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) in te `0` stellen om het beheer te delegeren aan de bibliotheek.

Voor bulkondersteuning in de .NET SDK is er geen verborgen gedrag. U de opties voor opnieuw proberen rechtstreeks configureren via de [CosmosClientOptions.MaxRetryAttemptsOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) en [CosmosClientOptions.MaxRetryWaitTimeOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests).

> [!NOTE]
> In gevallen waarin de ingerichte aanvraageenheden veel lager zijn dan de verwachte op basis van de hoeveelheid gegevens, u overwegen deze in te stellen op hoge waarden. De bulk operatie zal langer duren, maar het heeft een hogere kans van volledig slagen als gevolg van de hogere pogingen.

## <a name="performance-improvements"></a>Prestatieverbeteringen

Net als bij andere bewerkingen met de .NET SDK resulteert het gebruik van de stream-API's in betere prestaties en voorkomt onnodige serialisatie. 

Het gebruik van stream-API's is alleen mogelijk als de aard van de gegevens die u gebruikt overeenkomt met die van een stroom bytes (bijvoorbeeld bestandsstromen). In dergelijke gevallen `CreateItemStreamAsync`verhoogt `ReplaceItemStreamAsync`het `DeleteItemStreamAsync` gebruik van `ResponseMessage` de , `ItemResponse`- of methoden en het werken met (in plaats van ) de doorvoer die kan worden bereikt.

## <a name="next-steps"></a>Volgende stappen

* Zie het azure [cosmos DB SDK-artikel](sql-api-sdk-dotnet.md) voor meer informatie over de .NET SDK-releases.
* Download de volledige [migratiebroncode](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) van GitHub.
* [Extra bulkmonsters op GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)

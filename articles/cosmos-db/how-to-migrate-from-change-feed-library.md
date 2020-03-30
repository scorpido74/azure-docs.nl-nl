---
title: Migreren van de bibliotheek voor de wijzigingsfeedprocessor naar de Azure Cosmos DB .NET V3 SDK
description: Meer informatie over het migreren van uw toepassing van het gebruik van de bibliotheek voor de wijzigingsfeedprocessor naar de Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: 9570a8512e3437b12ecce2ef0c708a74a8806482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588880"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Migreren van de bibliotheek voor de wijzigingsfeedprocessor naar de Azure Cosmos DB .NET V3 SDK

In dit artikel worden de vereiste stappen beschreven om de code van een bestaande toepassing te migreren die de [bibliotheek voor de wijzigingsfeedprocessor](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) gebruikt naar de [wijzigingsfeedfunctie](change-feed.md) in de nieuwste versie van de .NET SDK (ook wel .NET V3 SDK genoemd).

## <a name="required-code-changes"></a>Vereiste codewijzigingen

De .NET V3 SDK heeft verschillende wijzigingen, de volgende zijn de belangrijkste stappen om uw toepassing te migreren:

1. Zet `DocumentCollectionInfo` de `Container` instanties om in referenties voor de bewaakte en leases containers.
1. Aanpassingen die `WithProcessorOptions` worden gebruikt, `WithLeaseConfiguration` moeten `WithPollInterval` worden `WithStartTime` bijgewerkt om te `WithMaxItems` gebruiken en voor intervallen, voor de [begintijd](how-to-configure-change-feed-start-time.md)en om het maximale aantal items te definiëren.
1. Stel `processorName` de `GetChangeFeedProcessorBuilder` ingeschakeld in om `ChangeFeedProcessorOptions.LeasePrefix`de waarde `string.Empty` te matchen die is geconfigureerd op of gebruik het op een andere manier.
1. De wijzigingen worden niet `IReadOnlyList<Document>`meer geleverd als een `IReadOnlyCollection<T>` `T` , in plaats daarvan, het is een waar is een type dat u moet definiëren, is er geen basisitem klasse meer.
1. Als u de wijzigingen wilt afhandelen, hebt u geen implementatie meer nodig, in plaats daarvan moet u [een gemachtigde definiëren.](change-feed-processor.md#implementing-the-change-feed-processor) De gemachtigde kan een statische functie zijn of als u de status voor uitvoeringen moet behouden, u uw eigen klasse maken en een instantiemethode als gemachtigde doorgeven.

Als de oorspronkelijke code voor het bouwen van de wijzigingsfeedprocessor er bijvoorbeeld als volgt uitziet:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

De gemigreerde code ziet eruit als volgt:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

En de gemachtigde, kan een statische methode:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>Staat en leasecontainer

Net als bij de bibliotheek voor de wijzigingsfeedprocessor gebruikt de feedfunctie wijzigen in .NET V3 SDK een [leasecontainer](change-feed-processor.md#components-of-the-change-feed-processor) om de status op te slaan. De schema's zijn echter verschillend.

De SDK V3-feedprocessor detecteert elke oude bibliotheekstatus en migreert deze automatisch naar het nieuwe schema bij de eerste uitvoering van de gemigreerde toepassingscode. 

U de toepassing veilig stoppen met de oude code, de code migreren naar de nieuwe versie, de gemigreerde toepassing starten en eventuele wijzigingen die zijn doorgevoerd terwijl de toepassing is gestopt, worden opgehaald en verwerkt door de nieuwe versie.

> [!NOTE]
> Migraties van toepassingen die de bibliotheek gebruiken naar de .NET V3 SDK zijn eenrichtingsverkeer, omdat de status (leases) wordt gemigreerd naar het nieuwe schema. De migratie is niet achterwaarts compatibel.


## <a name="additional-resources"></a>Aanvullende bronnen

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Gebruiksvoorbeelden op GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Aanvullende voorbeelden op GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Volgende stappen

U nu verder gaan met meer informatie over de feedprocessor wijzigen in de volgende artikelen:

* [Overzicht van de feedprocessor van change](change-feed-processor.md)
* [De wijzigingenfeedschatting gebruiken](how-to-use-change-feed-estimator.md)
* [Starttijd van verwerker van wijzigingenfeed](how-to-configure-change-feed-start-time.md)

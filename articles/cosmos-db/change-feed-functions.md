---
title: Azure Cosmos DB Change feed gebruiken met Azure Functions
description: Azure Cosmos DB wijzigings feed gebruiken met Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 876fef2b597e9a7dfd896f2b9697378e745a07f3
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709817"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Op gebeurtenissen gebaseerde architecturen met serverloze architectuur met Azure Cosmos DB en Azure Functions

Azure Functions biedt de eenvoudigste manier om verbinding te maken met de [wijzigings feed](change-feed.md). U kunt kleine reactieve Azure Functions maken die automatisch worden geactiveerd voor elke nieuwe gebeurtenis in de wijzigings feed van de Azure Cosmos-container.

![Functies zonder server gebeurtenis op basis van gebeurtenissen die werken met de Azure Functions trigger voor Cosmos DB](./media/change-feed-functions/functions.png)

Met de [Azure functions trigger voor Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger)kunt u gebruikmaken van de schaal baarheid en betrouw bare gebeurtenis detectie functionaliteit van de [feed-processor](./change-feed-processor.md)zonder dat er een [werk infrastructuur](./change-feed-processor.md)hoeft te worden onderhouden. Focus op de logica van uw Azure-functie zonder dat u zich zorgen hoeft te maken over de rest van de pijp lijn voor gebeurtenis bronnen. U kunt de trigger zelfs combi neren met andere [Azure functions bindingen](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Op dit moment wordt de Azure Functions trigger voor Cosmos DB alleen ondersteund voor gebruik met de core-API (SQL).

## <a name="requirements"></a>Vereisten

U hebt het volgende nodig om een stroom op basis van een gebeurtenis op een server te implementeren:

* **De bewaakte container**: De bewaakte container is de Azure Cosmos-container die wordt bewaakt en de gegevens worden opgeslagen waaruit de wijzigings feed wordt gegenereerd. Eventuele toevoegingen en wijzigingen (bijvoorbeeld ruw) aan de bewaakte container worden weer gegeven in de wijzigings feed van de container.
* **De lease-container**: De lease-container houdt status over van meerdere en dynamische serverloze Azure-functie instanties en maakt dynamische schaling mogelijk. Deze lease container kan hand matig of automatisch worden gemaakt door de Azure Functions trigger voor Cosmos DB. Als u de lease-container automatisch wilt maken, stelt u de vlag *CreateLeaseCollectionIfNotExists* in de [configuratie](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)in. Gepartitioneerde lease containers moeten een `/id` partitie sleutel definitie hebben.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Uw Azure Functions-trigger maken voor Cosmos DB

Het maken van uw Azure-functie met een Azure Functions trigger voor Cosmos DB wordt nu ondersteund in alle Azure Functions IDE-en CLI-integraties:

* [Visual Studio-extensie](../azure-functions/functions-develop-vs.md) voor Visual Studio-gebruikers.
* [Visual Studio core-extensie](/azure/javascript/tutorial-vscode-serverless-node-01) voor gebruikers van Visual Studio code.
* En ten slotte [kern cli-hulp programma](../azure-functions/functions-run-local.md#create-func) voor een platformoverschrijdende IDE neutraal-ervaring.

## <a name="run-your-trigger-locally"></a>Uw trigger lokaal uitvoeren

U kunt uw [Azure-functie lokaal](../azure-functions/functions-develop-local.md) uitvoeren met de [Azure Cosmos DB-emulator](./local-emulator.md) voor het maken en ontwikkelen van zonder een Azure-abonnement gemaakte stromen op basis van gebeurtenissen.

Als u live-scenario's in de Cloud wilt testen, kunt u [Cosmos DB gratis proberen](https://azure.microsoft.com/try/cosmosdb/) zonder dat u een credit card of een Azure-abonnement nodig hebt.

## <a name="next-steps"></a>Volgende stappen

U kunt nu door gaan met meer informatie over wijzigings invoer in de volgende artikelen:

* [Overzicht van wijzigings feed](change-feed.md)
* [Manieren om een wijzigings feed te lezen](read-change-feed.md)
* [Met behulp van de change feed processor-bibliotheek](change-feed-processor.md)
* [Werken met de processor bibliotheek voor wijzigings invoer](change-feed-processor.md)
* [Serverloze database Computing met Azure Cosmos DB en Azure Functions](serverless-computing-database.md)

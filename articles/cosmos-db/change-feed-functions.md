---
title: Azure Cosmos DB-wijzigingsfeed gebruiken met Azure-functies
description: Gebruik Azure-functies om verbinding te maken met de feed voor wijzigingen in Azure Cosmos DB. Later u reactieve Azure-functies maken die op elke nieuwe gebeurtenis worden geactiveerd.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 7a74635551d8416bf60689b1f1403f29883e81bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851366"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Op serverloze gebeurtenissen gebaseerde architecturen met Azure Cosmos DB en Azure-functies

Azure Functions biedt de eenvoudigste manier om verbinding te maken met de [wijzigingsfeed.](change-feed.md) U kleine reactieve Azure-functies maken die automatisch worden geactiveerd op elke nieuwe gebeurtenis in de wijzigingsfeed van uw Azure Cosmos-container.

![Serverloze gebeurtenisgebaseerde functies die werken met de trigger voor Azure-functies voor Cosmos DB](./media/change-feed-functions/functions.png)

Met de [Azure Functions-trigger voor Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)u gebruikmaken van de schaalbaarheid en betrouwbare functionaliteit voor gebeurtenisdetectie van de Change Feed [Processor,](./change-feed-processor.md)zonder dat u de [infrastructuur van werknemers](./change-feed-processor.md)hoeft te onderhouden. Richt u op de logica van uw Azure-functie zonder u zorgen te maken over de rest van de pijplijn voor het vinden van gebeurtenissen. U de Trigger zelfs mengen met andere [Azure-functiesbindingen.](../azure-functions/functions-triggers-bindings.md#supported-bindings)

> [!NOTE]
> Momenteel wordt de Trigger van Azure-functies voor Cosmos DB alleen ondersteund voor gebruik met de Core (SQL)-API.

## <a name="requirements"></a>Vereisten

Als u een serverloze gebeurtenisstroom wilt implementeren, hebt u het:

* **De bewaakte container**: de bewaakte container is de Azure Cosmos-container die wordt gecontroleerd en slaat de gegevens op waaruit de wijzigingsfeed wordt gegenereerd. Eventuele inserts, updates van de bewaakte container worden weerspiegeld in de wijzigingsfeed van de container.
* **De leasecontainer**: de leasecontainer behoudt de status voor meerdere en dynamische azure-functie-exemplaren zonder server en maakt dynamische schaling mogelijk. Deze leasecontainer kan handmatig of automatisch worden gemaakt door de Trigger voor Azure-functies voor Cosmos DB. Als u de leasecontainer automatisch wilt maken, stelt u de vlag *CreateLeaseCollectionIfNotExists* in de [configuratie](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)in. Partitieleasecontainers zijn vereist om `/id` een definitie van partitiesleutel te hebben.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>De trigger van Azure-functies maken voor Cosmos DB

Het maken van uw Azure-functie met een Azure-functietrigger voor Cosmos DB wordt nu ondersteund voor alle Azure Functions IDE- en CLI-integraties:

* [Visual Studio-extensie](../azure-functions/functions-develop-vs.md) voor Gebruikers van Visual Studio.
* [Visual Studio Code Extension](/azure/javascript/tutorial-vscode-serverless-node-01) for Visual Studio Code users.
* En tenslotte [Core CLI tooling](../azure-functions/functions-run-local.md#create-func) voor een cross-platform IDE agnostische ervaring.

## <a name="run-your-trigger-locally"></a>Voer uw trigger lokaal uit

U uw [Azure-functie lokaal](../azure-functions/functions-develop-local.md) uitvoeren met de [Azure Cosmos DB Emulator](./local-emulator.md) om uw serverloze gebeurtenisstromen te maken en te ontwikkelen zonder een Azure-abonnement of kosten te maken.

Als u live scenario's in de cloud wilt testen, u [Cosmos DB gratis uitproberen](https://azure.microsoft.com/try/cosmosdb/) zonder dat een creditcard of Azure-abonnement vereist is.

## <a name="next-steps"></a>Volgende stappen

U nu meer te weten komen over de wijzigingsfeed in de volgende artikelen:

* [Overzicht van wijzigingsfeed](change-feed.md)
* [Manieren om wijzigingsfeed te lezen](read-change-feed.md)
* [De feedprocessorbibliotheek wijzigen gebruiken](change-feed-processor.md)
* [Werken met de bibliotheek voor de feedprocessor wijzigen](change-feed-processor.md)
* [Serverloze databasecomputing met Azure Cosmos DB- en Azure-functies](serverless-computing-database.md)

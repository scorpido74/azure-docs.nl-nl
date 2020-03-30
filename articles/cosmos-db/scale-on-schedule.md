---
title: Azure Cosmos DB schalen volgens een planning met azure-functiestimer
description: Meer informatie over het schalen van wijzigingen in doorvoer in Azure Cosmos DB met PowerShell- en Azure-functies.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: 68ba40ea212c061fa5c8bbddc47ea0dfc6d8caa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75935166"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Azure Cosmos DB-doorvoer schalen met de trigger Azure Functions Timer

De prestaties van een Azure Cosmos-account zijn gebaseerd op de hoeveelheid ingerichte doorvoer uitgedrukt in aanvraageenheden per seconde (RU/s). De inrichting is op een tweede granulariteit en wordt gefactureerd op basis van de hoogste RU /s per uur. Dit ingerichte capaciteitsmodel stelt de service in staat om een voorspelbare en consistente doorvoer, gegarandeerde lage latentie en hoge beschikbaarheid te bieden. De meeste productieworkloads deze functies. In ontwikkel- en testomgevingen waar Azure Cosmos DB alleen tijdens werkuren wordt gebruikt, u de doorvoer in de ochtend echter opschalen en 's avonds na werktijd weer omlaag.

U de doorvoer instellen via [Azure Resource Manager Templates,](resource-manager-samples.md) [Azure CLI](cli-samples.md)en [PowerShell,](powershell-samples-sql.md)voor Core (SQL) API-accounts of met behulp van de taalspecifieke Azure Cosmos DB SDKs. Het voordeel van het gebruik van Resource Manager Templates, Azure CLI of PowerShell is dat ze alle Azure Cosmos DB-model API's ondersteunen.

## <a name="throughput-scheduler-sample-project"></a>Voorbeeldproject doorvoerplanner

Om het proces voor het schalen van Azure Cosmos DB volgens een planning te vereenvoudigen, hebben we een voorbeeldproject gemaakt met de naam [Azure Cosmos-doorvoerplanner.](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) Dit project is een Azure Functions-app met twee timertriggers: "ScaleUpTrigger" en "ScaleDownTrigger". De triggers voeren een PowerShell-script uit dat de `resources.json` doorvoer instelt op elke resource zoals gedefinieerd in het bestand in elke trigger. De ScaleUpTrigger is geconfigureerd om te draaien op 8 AM UTC en de ScaleDownTrigger is geconfigureerd `function.json` om uit te voeren op 6 PM UTC en deze tijden kunnen eenvoudig worden bijgewerkt in het bestand voor elke trigger.

U dit project lokaal klonen en wijzigen om de Azure Cosmos DB-resources op te geven die op en neer moeten worden opgeschaald en de planning die moet worden uitgevoerd. Later u het implementeren in een Azure-abonnement en beveiligen met behulp van managed service-identiteit met [RBAC-machtigingen (Role-based Access Control)](role-based-access-control.md) met de rol 'Azure Cosmos DB-operator' om doorvoer in te stellen op uw Azure Cosmos-accounts.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie en download het voorbeeld van [Azure Cosmos DB-doorvoerplanner](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler).

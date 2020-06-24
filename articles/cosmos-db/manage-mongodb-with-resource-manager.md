---
title: Resource Manager-sjablonen voor de Azure Cosmos DB-API voor MongoDB
description: Gebruik Azure Resource Manager sjablonen om Azure Cosmos DB-API voor MongoDB te maken en te configureren.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: b35f78385b85a7514db4d69a0f209c37e3af0d7f
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85262375"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Azure Cosmos DB MongoDB-API-resources beheren met Azure Resource Manager sjablonen

In dit artikel leert u hoe u Azure Resource Manager sjablonen kunt gebruiken om uw Azure Cosmos DB-accounts te implementeren en beheren voor MongoDB-API,-data bases en-verzamelingen.

In dit artikel vindt u voor beelden van de Azure Cosmos DB-API voor MongoDB, om voor beelden te vinden voor andere typen API-accounts: gebruik Azure Resource Manager sjablonen met de API van Azure Cosmos DB voor [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [tabel](manage-table-with-resource-manager.md) artikelen.

> [!IMPORTANT]
>
> * Account namen zijn beperkt tot 44 tekens, alle kleine letters.
> * Als u de doorvoer waarden wilt wijzigen, implementeert u de sjabloon opnieuw met de bijgewerkte RU/s.
> * Wanneer u locaties toevoegt aan of verwijdert uit een Azure Cosmos-account, kunt u niet tegelijkertijd andere eigenschappen wijzigen. Deze bewerkingen moeten afzonderlijk worden uitgevoerd.

Als u een van de onderstaande Azure Cosmos DB resources wilt maken, kopieert u de volgende voorbeeld sjabloon naar een nieuw JSON-bestand. U kunt optioneel een JSON-bestand voor para meters maken dat moet worden gebruikt bij het implementeren van meerdere exemplaren van dezelfde resource met verschillende namen en waarden. Er zijn veel manieren om Azure Resource Manager sjablonen te implementeren, waaronder, [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [Azure cli](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) en [github](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-mongodb-with-autoscale-provisioned-throughput"></a>Azure Cosmos-account voor MongoDB met automatisch schalen ingerichte door Voer

Met deze sjabloon wordt een Azure Cosmos-account gemaakt voor MongoDB API (3,2 of 3,6) met twee verzamelingen die de door Voer van automatisch schalen op database niveau delen. Deze sjabloon is ook beschikbaar voor één klik op implementeren vanuit de Azure Quick Start-sjablonen galerie.

[![Implementeren in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-mongodb-with-standard-provisioned-throughput"></a>Azure Cosmos-account voor MongoDB met een standaard ingerichte door Voer

Met deze sjabloon wordt een Azure Cosmos-account gemaakt voor MongoDB-API (3,2 of 3,6) met twee verzamelingen die de standaard doorvoer van 400 RU/s (hand matig) op database niveau delen. Deze sjabloon is ook beschikbaar voor één klik op implementeren vanuit de Azure Quick Start-sjablonen galerie.

[![Implementeren in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-mongodb%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

## <a name="next-steps"></a>Volgende stappen

Hier volgen enkele aanvullende bronnen:

* [Documentatie voor Azure Resource Manager](/azure/azure-resource-manager/)
* [Resource provider-schema Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
* [Quick Start-sjablonen Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Veelvoorkomende fouten bij Azure Resource Manager implementatie oplossen](../azure-resource-manager/templates/common-deployment-errors.md)

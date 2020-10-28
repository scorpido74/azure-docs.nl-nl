---
title: Voorbeelden van Azure PowerShell voor Azure Cosmos DB Table-API
description: Download de Azure PowerShell-voorbeelden voor het uitvoeren van verschillende algemene taken Azure Cosmos DB Table-API
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: d48d16d13bf35636950366cba7cd946f01afcff0
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282924"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-table-api"></a>Voorbeelden van Azure PowerShell voor Azure Cosmos DB Table-API

De volgende tabel bevat koppelingen naar veelgebruikte Azure PowerShell-scripts voor Azure Cosmos DB. Gebruik de links aan de rechterkant om te navigeren naar voorbeelden voor specifieke API's. Algemene voorbeelden zijn hetzelfde voor alle API's. Referentiepagina's voor alle PowerShell-cmdlets voor Azure Cosmos DB zijn beschikbaar in de [naslaginformatie voor Azure PowerShell](/powershell/module/az.cosmosdb). Controleer regelmatig of er updates zijn van `Az.CosmosDB`. U kunt deze PowerShell-voorbeelden ook forken voor Cosmos DB vanaf onze GitHub-opslagplaats, te weten [Cosmos DB PowerShell Samples op GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Algemene voorbeelden

|Taak | Beschrijving |
|---|---|
|[Een account bijwerken](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Werk het standaard consistentieniveau van een Cosmos DB-account bij. |
|[De regio’s van een account bijwerken](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Werk de regio’s van een Cosmos DB-account bij. |
|[Failoverprioriteit wijzigen of failover activeren](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wijzig de prioriteit van de regionale failover van een Azure Cosmos-account of activeer een handmatige failover. |
|[Accountsleutels of verbindingsreeksen](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Haal de primaire en secundaire sleutels en verbindingsreeksen op of genereer opnieuw een accountsleutel van een Azure Cosmos DB-account. |
|[Een Cosmos-account maken met een IP-firewall](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een Azure Cosmos DB-account waarvoor IP-firewall is ingeschakeld. |
|||

## <a name="table-api-samples"></a>Voorbeelden van Table-API

|Taak | Beschrijving |
|---|---|
|[Een account en een tabel maken](scripts/powershell/table/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hiermee wordt een Azure Cosmos-account en -tabel gemaakt. |
|[Een account en tabel maken met automatische schaalaanpassing](scripts/powershell/table/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hiermee maakt u een Azure Cosmos-account en -tabel met automatische schaalaanpassing. |
|[Tabellen weergeven of ophalen](scripts/powershell/table/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Geef tabellen weer of haal deze op. |
|[Doorvoerbewerkingen](scripts/powershell/table/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Doorvoerbewerkingen voor een tabel, waaronder ophalen, bijwerken en migreren tussen doorvoer met automatische schaalaanpassing en standaarddoorvoer. |
|[Resources vergrendelen tegen verwijdering](scripts/powershell/table/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Voorkomen dat resources worden verwijderd met een resourcevergrendeling. |
|||

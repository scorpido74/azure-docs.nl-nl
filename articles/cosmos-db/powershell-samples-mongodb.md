---
title: Azure PowerShell-voorbeelden voor Azure Cosmos DB-API voor MongoDB
description: De Azure PowerShell-voorbeelden verschillende algemene taken laten uitvoeren in de Azure Cosmos DB-API voor MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2020
ms.author: mjbrown
ms.openlocfilehash: 697dc68018ced08e22efdc179f84a2c968f953a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563867"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>Azure PowerShell-voorbeelden voor Azure Cosmos DB-API voor MongoDB

De volgende tabel bevat koppelingen naar Azure PowerShell-voorbeeldscripts voor de Azure Cosmos DB-API voor MongoDB.

> [!NOTE]
> Op dit moment kunt u alleen de 3.2-versie maken (dat wil zeggen, accounts die gebruikmaken van het eindpunt in de indeling `*.documents.azure.com`) van de Azure Cosmos DB-API voor MongoDB-accounts met behulp van PowerShell, CLI en Resource Manager-sjablonen. Als u de 3.6-versie van accounts wilt maken, gebruikt u in plaats daarvan Azure Portal.

> [!NOTE]
> In de voorbeelden wordt gebruikgemaakt van beheer-cmdlets voor [AZ. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb). Controleer regelmatig of er updates zijn van `Az.CosmosDB`.

|Taak | Beschrijving |
|---|---|
|[Een account, database en verzameling maken](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hiermee maakt u een Azure Cosmos-account, -database en -verzameling. |
|[Databases of verzamelingen in een lijst weergeven of ophalen](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Geef de database of verzameling in een lijst weer of haal deze op. |
|[RU/s ophalen](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| RU/s ophalen voor een database of verzameling. |
|[RU/s bijwerken](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Werk RU/s bij voor een database of verzameling. |
|[Een account bijwerken of een regio toevoegen](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Voeg een regio toe aan een Cosmos-account. Kan ook worden gebruikt om andere accounteigenschappen te wijzigen, maar deze moeten gescheiden zijn van wijzigingen in regio's. |
|[Failoverprioriteit wijzigen of failover activeren](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wijzig de prioriteit van de regionale failover van een Azure Cosmos-account of activeer een handmatige failover. |
|[Accountsleutels of verbindingsreeksen](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Haal de primaire en secundaire sleutels en verbindingsreeksen op of genereer opnieuw een accountsleutel van een Azure Cosmos-account. |
|[Een Cosmos-account maken met een IP-firewall](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een Azure Cosmos-account waarvoor IP-firewall is ingeschakeld. |
|[Resources vergrendelen tegen verwijdering](scripts/powershell/mongodb/powershell-mongodb-lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Voorkomen dat resources worden verwijderd met een resourcevergrendeling. |
|||

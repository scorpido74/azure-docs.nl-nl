---
title: Azure PowerShell-voor beelden voor Azure Cosmos DB-MongoDB-API
description: Bekijk de Azure PowerShell-voor beelden om verschillende algemene taken uit te voeren in de API van Azure Cosmos DB voor MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 810161268df405d84f6c190d4d7f3b67f2a1def8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80366202"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>Azure PowerShell-voor beelden voor Azure Cosmos DB MongoDB-API

De volgende tabel bevat koppelingen naar voor beelden Azure PowerShell scripts voor Azure Cosmos DB voor MongoDB-API.

> [!NOTE]
> Op dit moment kunt u alleen 3,2-versie maken (dat wil zeggen, accounts die gebruikmaken `*.documents.azure.com`van het eind punt in de indeling) van de API van Azure Cosmos DB voor MongoDb-accounts met behulp van Power shell, CLI en Resource Manager-sjablonen. Als u 3,6-versie van accounts wilt maken, gebruikt u Azure Portal in plaats daarvan.

> [!NOTE]
> De voor beelden gebruiken [AZ. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) Management-cmdlets. Houd er rekening mee `Az.CosmosDB` dat de cmdlets nog in Preview zijn en kunnen worden gewijzigd voordat de release wordt uitgebracht. Controleer `Az.CosmosDB` regel matig of er updates zijn.

| | |
|---|---|
|[Een account, data base en verzameling maken](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hiermee maakt u een Azure Cosmos-account,-data base en-verzameling. |
|[Data bases of verzamelingen weer geven of ophalen](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Data Base of verzameling weer geven of ophalen. |
|[Ophalen van RU/s](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ophalen van RU/s voor een Data Base of verzameling. |
|[RU/s bijwerken](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Update RU/s voor een Data Base of verzameling. |
|[Een account bijwerken of een regio toevoegen](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Voeg een regio toe aan een Cosmos-account. Kan ook worden gebruikt om andere account eigenschappen te wijzigen, maar deze moeten gescheiden zijn van wijzigingen in regio's. |
|[Failover-prioriteit of trigger-failover wijzigen](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wijzig de prioriteit van de regionale failover van een Azure Cosmos-account of Activeer een hand matige failover. |
|[Account sleutels of verbindings reeksen](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Haal de primaire en secundaire sleutels, verbindings reeksen of een account sleutel van een Azure Cosmos-account op. |
|[Een Cosmos-account maken met een IP-firewall](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een Azure Cosmos-account waarvoor IP-firewall is ingeschakeld. |
|||

---
title: Azure PowerShell-voorbeelden voor Azure Cosmos DB - MongoDB API
description: Download de Azure PowerShell-voorbeelden om verschillende veelvoorkomende taken uit te voeren in de API van Azure Cosmos DB voor MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 810161268df405d84f6c190d4d7f3b67f2a1def8
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366202"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>Azure PowerShell-voorbeelden voor Azure Cosmos DB MongoDB API

De volgende tabel bevat koppelingen naar voorbeeld van Azure PowerShell-scripts voor Azure Cosmos DB voor MongoDB API.

> [!NOTE]
> Momenteel u alleen 3.2-versie maken (dat wil `*.documents.azure.com`zeggen accounts met behulp van het eindpunt in de indeling) van de API van Azure Cosmos DB voor MongoDB-accounts met behulp van PowerShell-, CLI- en Resource Manager-sjablonen. Als u een 3.6-versie van accounts wilt maken, gebruikt u in plaats daarvan Azure-portal.

> [!NOTE]
> De monsters maken gebruik van [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) management cmdlets. Houd er `Az.CosmosDB` rekening mee dat de cmdlets nog in de preview staan en voor de release kunnen veranderen. Controleer regelmatig op `Az.CosmosDB` updates.

| | |
|---|---|
|[Een account, database en verzameling maken](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hiermee maakt u een Azure Cosmos-account, -database en -verzameling. |
|[Databases of verzamelingen aanbieden of openen](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Lijst of krijgen database of verzameling. |
|[Ontvang RU/s](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ontvang RU/s voor een database of verzameling. |
|[RU/s bijwerken](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| RU/s bijwerken voor een database of verzameling. |
|[Een account bijwerken of een regio toevoegen](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Een regio toevoegen aan een Cosmos-account. Kan ook worden gebruikt om andere accounteigenschappen te wijzigen, maar deze moeten gescheiden zijn van wijzigingen in regio's. |
|[Prioriteit voor failover wijzigen of failover activeren](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Wijzig de regionale failoverprioriteit van een Azure Cosmos-account of activeer een handmatige failover. |
|[Accountsleutels of verbindingstekenreeksen](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ontvang primaire en secundaire sleutels, verbindingstekenreeksen of regenereerde een accountsleutel van een Azure Cosmos-account. |
|[Een Cosmos-account maken met IP-firewall](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Maak een Azure Cosmos-account met IP Firewall ingeschakeld. |
|||

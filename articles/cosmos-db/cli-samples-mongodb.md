---
title: Voorbeelden van Azure CLI voor Azure Cosmos DB MongoDB API
description: Voorbeelden van Azure CLI voor Azure Cosmos DB MongoDB API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: 88b795b52955a6bd323e7a900c0cd62dab1dd2d4
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85262936"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-mongodb-api"></a>Voorbeelden van Azure CLI voor Azure Cosmos DB MongoDB API

De volgende tabel bevat koppelingen naar Azure CLI-voorbeeldscripts voor Azure Cosmos DB MongoDB API. Referentiepagina's voor alle Azure Cosmos DB CLI-opdrachten zijn beschikbaar in de [naslaginformatie voor Azure CLI](/cli/azure/cosmosdb). Alle Azure Cosmos DB CLI-voorbeeldscripts vindt u in de [documentatie van Azure Cosmos DB CLI GitHub-opslagplaats](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

> [!NOTE]
> Op dit moment kunt u alleen de 3.2-versie maken (dat wil zeggen, accounts die gebruikmaken van het eindpunt in de indeling `*.documents.azure.com`) van de Azure Cosmos DB-API voor MongoDB-accounts met behulp van PowerShell, CLI en Resource Manager-sjablonen. Als u de 3.6-versie van accounts wilt maken, gebruikt u in plaats daarvan Azure Portal.

| |  |
|---|---|
| [Een Azure Cosmos-account, -database en -verzameling maken](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Hiermee maakt u een Azure Cosmos DB MongoDB-API-account, -database en -verzameling voor MongoDB-API. |
| [Doorvoer wijzigen](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | RU/s bijwerken voor een database en verzameling.|
| [Regio's toevoegen of er een failover voor uitvoeren](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Een regio toevoegen, de failoverprioriteit wijzigen, een handmatige failover activeren.|
| [Accountsleutels en verbindingsreeksen](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Accountsleutels en alleen-lezensleutels weergeven, sleutels opnieuw genereren en verbindingsreeksen weergeven.|
| [Beveiligen met IP-firewall](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Een Cosmos-account maken met een geconfigureerde IP-firewall.|
| [Nieuw account beveiligen met service-eindpunten](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Een Cosmos-account maken en beveiligen met service-eindpunten.|
| [Bestaand account beveiligen met service-eind punten](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Een Cosmos-account bijwerken om het te beveiligen met service-eindpunten wanneer het subnet uiteindelijk is geconfigureerd.|
| [Resources vergrendelen tegen verwijdering](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Voorkomen dat resources worden verwijderd met een resourcevergrendeling.|
|||
